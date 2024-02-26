## 作业前言

在本次作业中，我们只关注两种死代码：**不可达代码**（unreachable code）和**无用赋值**（dead assignment）

可以结合中科大的编译原理实验视频来学习

- 【6.1 常量传播】https://www.bilibili.com/video/BV1RC411x7Ua

中科大这边给出了一个常量传播（带死代码检测）算法的伪代码，如下

```python
# 沿CFG对应支配树从entry开始按BFS顺序遍历每个基本块
foreach block in blocks do:
    # 对基本块扫描，遍历每个指令
    foreach instr in block do:
        # 如果是常量赋值或常量计算的结果
        if isConstantAssignment(instr):
            # 替换为强制跳转，并标记另一个分支不可达
            propConstToRef(instr)
        # 如果是常量值的分支条件
        elif isBranchAndConstPredicate(instr):
            # 替换为强制跳转，并标记另一个分支不可达
            changeCOndBranchToAbsBranchAndMark(instr)
        # 如果是 Phi 函数的指令
        elif isPhi(instr):
            # 如果有一个基本块不可达，或者两个基本块的值相同
            if isOneBLockUnreachable(instr) or isSameValPre(instr):
                # 将可达块的值传播到对该指令的引用
                propPhiToRef(instr)
```

## 作业实现

| 函数 | 文件路径 |
| :--- | :--- |
| CPFact ConstantPropagation.newBoundaryFact(CFG\<Stmt\>) | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| CPFact ConstantPropagation.newInitialFact() | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| void ConstantPropagation.meetInto\(CPFact, CPFact\) | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| Value ConstantPropagation.meetValue\(Value, Value\) | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| boolean ConstantPropagation.transferNode(Stmt, CPFact, CPFact) | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| Value ConstantPropagation.evaluate(Exp, CPFact) | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| class WorkListSolver<Node, Fact> extends Solver<Node, Fact> | analysis/dataflow/solver/WorkListSolver.java |


