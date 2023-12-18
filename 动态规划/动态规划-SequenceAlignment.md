# Sequence alignment algorithms

[Sequence alignment - Wikipedia](https://en.wikipedia.org/wiki/Sequence_alignment#Global_and_local_alignments)

[Needleman–Wunsch algorithm - Wikipedia](https://en.wikipedia.org/wiki/Needleman%E2%80%93Wunsch_algorithm)

<img src="https://raw.githubusercontent.com/LiangsLi/tuchuang/master/picgo/20220526172905.png" alt="image-20220526172905037"  />

## 实现

```cpp
std::vector<std::pair<char32_t, char32_t>> alignTextPair(
    const std::string &originalText,
    const std::vector<std::map<std::string, std::string>> &wordInfoList,
    int matchScore,
    int mismatchPenalty,
    int gapPenalty) {
    std::u32string originalTextU32 = UTF::utf32Convert.from_bytes(originalText);
    std::u32string targetTextU32;
    for (auto &wordInfo : wordInfoList) {
        targetTextU32 += UTF::utf32Convert.from_bytes(wordInfo.at("word"));
    }
    int rows = originalTextU32.length() + 1;
    int cols = targetTextU32.length() + 1;
    // 初始化表格(得分矩阵)，seq_1_len+1 * seq_2_len+1
    Matrix<int> scoreMatrix(rows, cols);
    // 填充第0行和第0列， 具体取值取决于 gap（Insert 或 Delete） 的得分
    for (int i {1}; i < rows; ++i) {
        scoreMatrix[i][0] = i * gapPenalty;
    }
    for (int j {1}; j < cols; ++j) {
        scoreMatrix[0][j] = j * gapPenalty;
    }
    //   "自下而上" 计算 得分矩阵
    for (int i {0}; i < rows - 1; ++i) {
        for (int j {0}; j < cols - 1; ++j) {
            // three possible move:
            //    (1)diagonal : match/mismatch,
            //    (2)down : gap,
            //    (3)right : gap,
            if (originalTextU32[i] == targetTextU32[j]) {
                auto [maxScore, maxIdx] = findMaxThree(scoreMatrix[i][j] + matchScore,       // diagonal(match)
                                                       scoreMatrix[i][j + 1] + gapPenalty,   // down
                                                       scoreMatrix[i + 1][j] + gapPenalty);  // right
                scoreMatrix[i + 1][j + 1] = maxScore;
            } else {
                auto [maxScore, maxIdx] = findMaxThree(scoreMatrix[i][j] + mismatchPenalty,  // diagonal(mismatch)
                                                       scoreMatrix[i][j + 1] + gapPenalty,   // down
                                                       scoreMatrix[i + 1][j] + gapPenalty);  // right
                scoreMatrix[i + 1][j + 1] = maxScore;
            }
        }
    }
    // 从右下角到左上角 "回溯"
    int i {rows - 1};
    int j {cols - 1};
    std::vector<std::pair<char32_t, char32_t>> alignedCharPairs;
    alignedCharPairs.reserve(rows + cols);
    while (i > 0 || j > 0) {
        int moveIdx;
        if (i > 0 && j > 0) {
            auto maxRes = findMaxThree(scoreMatrix[i - 1][j - 1],  // 上对角线，match/mismatch
                                       scoreMatrix[i - 1][j],   // right, 在 seq2 中插入 EmptyChar (表示空字符)
                                       scoreMatrix[i][j - 1]);  // up, 在 seq1 中插入 EmptyChar
            moveIdx = maxRes.second;                            // 取值可能为: 0，1，2
        } else if (i > 0 && j == 0) {
            moveIdx = 1;  // move to right
        } else if (i == 0 && j > 0) {
            moveIdx = 2;  // move to up
        } else {
            throw std::runtime_error("");
        }
        if (moveIdx == 0) {
            alignedCharPairs.emplace_back(originalTextU32[i - 1], targetTextU32[j - 1]);
            --i;
            --j;
        } else if (moveIdx == 1) {
            alignedCharPairs.emplace_back(originalTextU32[i - 1], EmptyChar);
            --i;
        } else if (moveIdx == 2) {
            alignedCharPairs.emplace_back(EmptyChar, targetTextU32[j - 1]);
            --j;
        } else {
            throw std::runtime_error("");
        }
    }
    return alignedCharPairs;
}
```



## 实现参考：

+ [needleman_wunsch_alignment.ipynb - Colaboratory](https://colab.research.google.com/github/zaneveld/full_spectrum_bioinformatics/blob/master/content/08_phylogenetic_trees/needleman_wunsch_alignment.ipynb)

+ [poke1024/pyalign: Fast and Versatile Alignments for Python](https://github.com/poke1024/pyalign)
+ [Martinsos/edlib: Lightweight, super fast C/C++ (& Python) library for sequence alignment using edit (Levenshtein) distance.](https://github.com/Martinsos/edlib)

## 相关问题：

+ [Dynamic time warping (动态时间规整) - Wikipedia](https://en.wikipedia.org/wiki/Dynamic_time_warping)

+ [Method of Four Russians - Wikipedia](https://en.wikipedia.org/wiki/Method_of_Four_Russians)