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

## 作业目录

其中部分函数在之前的作业中已有实现，该目录会添加已经实现的函数链接

### 基础目录

| 函数 | 文件路径 |
| :--- | :--- |
| [void initializeForward(CFG\<Node\>, DataflowResult\<Node, Fact\>)](../A1/README.md#solverinitializeforward) | analysis/dataflow/solver/Solver.java |
| [void initializeBackward(CFG\<Node\>, DataflowResult\<Node, Fact\>)](../A1/README.md#solverinitializebackward) | analysis/dataflow/solver/Solver.java |
| [void doSolveForward(CFG\<Node\>, DataflowResult\<Node, Fact\>)](南京大学%20-%20软件分析/A2/README.md#worklistsolver工作集算法) | analysis/dataflow/solver/WorkListSolver.java |
| [void doSolveBackward(CFG\<Node\>, DataflowResult\<Node, Fact\>)](#worklistsolverdosolvebackward) | analysis/dataflow/solver/WorkListSolver.java |

### 活跃变量分析目录

| 函数                                                                                                                                  | 文件路径                                                 |
| :---------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------- |
| [SetFact\<Var\> LiveVariableAnalysis.newInitialFact()](../A1/README.md#livevariableanalysisnewinitialfact)                          | analysis/dataflow/analysis/LiveVariableAnalysis.java |
| [SetFact\<Var\> LiveVariableAnalysis.newBoundaryFact(CFG\<Stmt\>)](../A1/README.md#livevariableanalysisnewboundaryfact)             | analysis/dataflow/analysis/LiveVariableAnalysis.java |
| [void LiveVariableAnalysis.meetInto(SetFact\<Var\>, SetFact\<Var\>)](../A1/README.md#livevariableanalysismeetinto)                  | analysis/dataflow/analysis/LiveVariableAnalysis.java |
| [boolean LiveVariableAnalysis.transferNode(Stmt, SetFact\<Var\>, SetFact\<Var\>)](../A1/README.md#livevariableanalysistransfernode) | analysis/dataflow/analysis/LiveVariableAnalysis.java |
### 常量传播目录

| 函数                                                                                                                  | 文件路径                                                          |
| :------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------ |
| [ConstantPropagation.newBoundaryFact(CFG\<Stmt\>)](南京大学%20-%20软件分析/A2/README.md#constantpropagationnewboundaryfact)            | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [CPFact ConstantPropagation.newInitialFact()](南京大学%20-%20软件分析/A2/README.md#constantpropagationnewinitialfact)                  | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [void ConstantPropagation.meetInto\(CPFact, CPFact\)](南京大学%20-%20软件分析/A2/README.md#constantpropagationmeetinto)                | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [Value ConstantPropagation.meetValue\(Value, Value\)](南京大学%20-%20软件分析/A2/README.md#constantpropagationmeetvalue)               | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [boolean ConstantPropagation.transferNode(Stmt, CPFact, CPFact)](南京大学%20-%20软件分析/A2/README.md#constantpropagationtransfernode) | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [Value ConstantPropagation.evaluate(Exp, CPFact)](南京大学%20-%20软件分析/A2/README.md#constantpropagationevaluate)                    | analysis/dataflow/analysis/constprop/ConstantPropagation.java |

## 作业实现

### WorkListSolver.doSolveBackward

```java
@Override
protected void doSolveBackward(CFG<Node> cfg, DataflowResult<Node, Fact> result) {
    // TODO - finish me
    Queue<Node> worklist = new LinkedList<>(cfg.getNodes());
    while (!worklist.isEmpty()) {
        Node node = worklist.poll();
        for (Node succ : cfg.getSuccsOf(node)) {
            this.analysis.meetInto(result.getInFact(node), result.getOutFact(succ));
        }
        if (this.analysis.transferNode(node, result.getInFact(node), result.getOutFact(node))) {
            worklist.addAll(cfg.getPredsOf(node));
        }
    }
}
```

