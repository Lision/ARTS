## ARTS - 0x04

![](../../arts.png)

### A

国区的每日一题，617 - 合并二叉树，Easy

先是老实巴交的写了一边 malloc 的，说实话如果 return 之后外面没有 free 其实算是有内存泄漏...可能是这个原因导致国区验证巨慢，一度让我怀疑是不是靠这个卖会员了（开会员有极速判题的功能）。

验证结果挺惨的：

![](leetcode_first.png)

想了一下偷鸡把入参拿来直接修改反馈，这次空间复杂度 O(0) 了，成绩比较好看：

![](leetcode_second.png)

国际版这道题在三年前做过，还是用 Swift 做的（额，实在不清楚为啥当时自己为啥会用 Swift 做题，猜测是为了学完用一用，应该是 Swift 2.x）。

![](3_years_ago.png)

### R

[LLVM Code Coverage Mapping Format](https://llvm.org/docs/CoverageMappingFormat.html)

#### 笔记

- 嵌入 LLVM IR
- 描述为 mapping formart，用于 code coverage tool 来 map

#### mapping data

- 编译器编译源文件时 flag -fcoverage-mapping，生成 mapping infomation 来描述 mapping（从 source ranges 到 profiling instrumentation counters）。这个 mapping 嵌入 LLVM IR 并在链接之后添加到最终执行文件中。
- llvm-cov 可以用 mapping information（从上一步执行文件中取出）来关联执行计数（the value of profiling instrumentation counters）和源文件，之后可以生成多样化的代码覆盖率报告。

- mapping data format 是普适的 「universal format」，可以被任何其他前端（不仅仅是 Clang）使用。
- 它还提供尽可能小的 coverage data 来减少 IR 和目标文件的尺寸（例如 func 中的临时变量声明等语句会被 grouped 之后就可以仅记录这一片区域的执行次数）

#### 进阶概念

mapping data 是 func 级别的，每一个 func 都应该生成 mapping data 来提供源码和 func 的执行关联。

#### Mapping Region

- func's mapping data 包含一组 mapping regions
- 一个 mapping region 存储着这个 region 内包含的源码范围，file id，coverage mapping counter 以及 kind

kinds：

- Code regions 关联部分源码以及 counters，由主要的 mapping regions 组成，用于 code coverage tool 计算 counters for lines，高亮未覆盖到的 region of code，获得多样化的 code coverage 分析 for a func.
- Skipped regions 用于表示被 Clang's preprocessor 跳过的源码区域，标记未执行
- Expansion regions 用于关联 Clang's macro 展开，带有额外属性 - expanded file id，用于关联被执行代码的 mapping regions（通过检查 file id），不用计数，可以在 file id 关联的地方直接找到执行次数

#### Source Range

- mappding region 的开始和结束位置
- 位置由 line 和 clolumn 组成

#### File ID

- int，定位文件或宏展开
- Clang 可以提供 macros 内部的代码映射信息

#### Counter

- coverage mapping counter 可以表示 profile instrumentat counter。
- region 的执行次数是通过查询 profile instrumentat counter 拿到的。
- 类似 if else 这种会算 if 的执行次数，通过 if 前的执行总次数 - if 执行次数计算 else 的执行次数

#### LLVM IR Representation

coverage mapping data 存储在 LLVM IR，用一个全局静态结构变量 __llvm_coverage_mapping 在 IPSK_covmap section(i.e. “.lcovmap$M” on Windows and “__llvm_covmap” elsewhere).

coverage mapping variable 被 Clang 生产出来，带有两个字段：

- Coverage mapping header
- 一个可选的压缩/简化过的在场 filenames list 在 translation unit

8-byte 对齐

### T

作为一个 Swift 萌新，在封装 SDK 的时候考虑照顾 Objective-C 的业务方，但是又不愿意把 Swift 代码 OC 化（那我还写什么 Swift 对不对）。一开始写了一个 `extension` s专门封装了 OC 风格的 API，后来发现可以用 `@objc(ocStyleApiName:::)` 这种形式的 @Annotation 直接达成目标。

### S

Emmmmm...上周说的选择工作机会的文章鸽了（至少这周是没写），马上轮到我公司内部分享了，准备把自己最近工程中做的事情以及之前的一次逆向甩锅的事情做内容分享（主要是想分享一些在当前公司做的事情，时间短可选的内容不多）。

咳咳..紫牛这个是公司内部的分享（也说不定能对外），肯定不会鸽就是了。
