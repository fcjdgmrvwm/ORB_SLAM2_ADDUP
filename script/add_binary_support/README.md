# 增加二进制词典支持

参考https://github.com/raulmur/ORB_SLAM2/pull/21/files

修改步骤如下：

a. 使用TemplatedVocabulary.h替换Thirdparty/DBoW2/DBoW2/下同名文件，其实就是为了增加loadFromBinaryFile和saveToBinaryFile这两个函数。

b. 将tools文件夹复制到根目录下，里面包含将txt格式的Voc转换成bin的工具。

c. 将System.cc中的bool bVocLoad = mpVocabulary->loadFromTextFile(strVocFile);修改成

```C++
#include <ctime>
    clock_t tStart = clock();
    bool bVocLoad = false;
    if (strVocFile.find(".txt") != string::npos)
        bVocLoad = mpVocabulary->loadFromTextFile(strVocFile);
    else
        bVocLoad = mpVocabulary->loadFromBinaryFile(strVocFile);
    printf("Vocabulary loaded in %.2fs\n", (double)(clock() - tStart)/CLOCKS_PER_SEC);
```

d. 在根目录下的CMakeList.txt文件后面增加

```Makefile
# Build tools
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${PROJECT_SOURCE_DIR}/tools)
add_executable(bin_vocabulary
tools/bin_vocabulary.cc)
target_link_libraries(bin_vocabulary ${PROJECT_NAME})
```

e. 在根目录下的build.sh文件后面增加

```Bash
cd ..

echo "Converting vocabulary to binary"
./tools/bin_vocabulary
```
