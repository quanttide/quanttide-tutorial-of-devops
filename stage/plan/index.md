# 计划

我们使用[ROADMAP](source/conventions/roadmap.md)和[TODO](source/conventions/todo.md)文件作为计划阶段的主要事实源。

其中ROADMAP主要用于指定版本规划，用于和人类的理解对齐；TODO主要用于分解ROADMAP为具体执行项，用于和AI的执行对齐。

这样设计的原因是，ROADMAP不能写的过于细致，否则人类会陷入大量的细节分散注意力，不好理解版本计划。所以ROADMAP应该大体和CHANGELOG的粒度对齐，同时也方便未来建立从计划到发布的循环。

TODO则必须至少要精确到具体的模块或文件。AI在执行方面很容易乱写乱放，因此在计划阶段指定其交付位置，和人类的模块划分和文件维护对齐。
