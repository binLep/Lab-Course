## 作业前言

在本次作业中，我们只关注两种死代码：**不可达代码**（unreachable code）和**无用赋值**（dead assignment）

本来在完成该作业的时候，我打算只用 `deadCode` 集合和一个循环队列来完成，也就是基于不可达性的遍历。但当我写到无用赋值的时候，我感觉还是要多加一个集合变量来完成实验，不然无法区分哪些是 **不可达代码** 和 **无用赋值**，会造成错误的代码块删除。

后续又发现，对于 callthrough 的 switch 语句，好像也是同理，所以我想那还不如直接去遍历可达代码块，之后用全集减去活跃代码集合，来得到死代码集合

## 作业目录

其中部分函数在之前的作业中已有实现，该目录会添加已经实现的函数链接

### 求解器目录

| 函数                                                                                                             | 文件路径                                         |
| :------------------------------------------------------------------------------------------------------------- | :------------------------------------------- |
| [void initializeForward(CFG\<Node\>, DataflowResult\<Node, Fact\>)](../A1/README.md#solverinitializeforward)   | analysis/dataflow/solver/Solver.java         |
| [void initializeBackward(CFG\<Node\>, DataflowResult\<Node, Fact\>)](../A1/README.md#solverinitializebackward) | analysis/dataflow/solver/Solver.java         |
| [void doSolveForward(CFG\<Node\>, DataflowResult\<Node, Fact\>)](../A2/README.md#worklistsolver工作集算法)          | analysis/dataflow/solver/WorkListSolver.java |
| [void doSolveBackward(CFG\<Node\>, DataflowResult\<Node, Fact\>)](#worklistsolverdosolvebackward)              | analysis/dataflow/solver/WorkListSolver.java |

### 活跃变量分析目录

| 函数                                                                                                                                  | 文件路径                                                 |
| :---------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------- |
| [SetFact\<Var\> LiveVariableAnalysis.newInitialFact()](../A1/README.md#livevariableanalysisnewinitialfact)                          | analysis/dataflow/analysis/LiveVariableAnalysis.java |
| [SetFact\<Var\> LiveVariableAnalysis.newBoundaryFact(CFG\<Stmt\>)](../A1/README.md#livevariableanalysisnewboundaryfact)             | analysis/dataflow/analysis/LiveVariableAnalysis.java |
| [void LiveVariableAnalysis.meetInto(SetFact\<Var\>, SetFact\<Var\>)](../A1/README.md#livevariableanalysismeetinto)                  | analysis/dataflow/analysis/LiveVariableAnalysis.java |
| [boolean LiveVariableAnalysis.transferNode(Stmt, SetFact\<Var\>, SetFact\<Var\>)](../A1/README.md#livevariableanalysistransfernode) | analysis/dataflow/analysis/LiveVariableAnalysis.java |

### 常量传播目录

| 函数                                                                                                                | 文件路径                                                          |
| :---------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ |
| [ConstantPropagation.newBoundaryFact(CFG\<Stmt\>)](../A2/README.md#constantpropagationnewboundaryfact)            | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [CPFact ConstantPropagation.newInitialFact()](../A2/README.md#constantpropagationnewinitialfact)                  | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [void ConstantPropagation.meetInto\(CPFact, CPFact\)](../A2/README.md#constantpropagationmeetinto)                | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [Value ConstantPropagation.meetValue\(Value, Value\)](../A2/README.md#constantpropagationmeetvalue)               | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [boolean ConstantPropagation.transferNode(Stmt, CPFact, CPFact)](../A2/README.md#constantpropagationtransfernode) | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [Value ConstantPropagation.evaluate(Exp, CPFact)](../A2/README.md#constantpropagationevaluate)                    | analysis/dataflow/analysis/constprop/ConstantPropagation.java |

### 死代码检测目录（本章）

| 函数                                                   | 文件路径                                              |
| :--------------------------------------------------- | :------------------------------------------------ |
| [Set\<Stmt\> analyze(IR)](#deadcodedetectionanalyze) | analysis/dataflow/analysis/DeadCodeDetection.java |

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
            this.analysis.meetInto(result.getInFact(succ), result.getOutFact(node));
        }
        if (this.analysis.transferNode(node, result.getInFact(node), result.getOutFact(node))) {
            worklist.addAll(cfg.getPredsOf(node));
        }
    }
}
```

## DeadCodeDetection.analyze

直接 BFS 添加可达节点，之后用节点全集减去活跃节点集就是死代码节点集

我这边只考虑两个点：

- 若是将测试样例进行嵌套，是否能正常得出检测结果
- 若是 Switch 语句不带 break，是否能正常得出结果

```java
@Override
public Set<Stmt> analyze(IR ir) {
    // obtain CFG
    CFG<Stmt> cfg = ir.getResult(CFGBuilder.ID);
    // obtain result of constant propagation
    DataflowResult<Stmt, CPFact> constants = ir.getResult(ConstantPropagation.ID);
    // obtain result of live variable analysis
    DataflowResult<Stmt, SetFact<Var>> liveVars = ir.getResult(LiveVariableAnalysis.ID);
    // keep statements (dead code) sorted in the resulting set
    Set<Stmt> deadCode = new TreeSet<>(Comparator.comparing(Stmt::getIndex));
    // Your task is to recognize dead code in ir and add it to deadCode
    // TODO - finish me
    // -*- 死代码分析 -*-
    // 这里有个重要的一点，得能判断嵌套中的不可达代码，所以这里用了工作集算法
    Set<Stmt> liveCode = new TreeSet<>(Comparator.comparing(Stmt::getIndex));
    Queue<Stmt> worklist = new LinkedList<>();
    worklist.add(ir.getStmt(0));
    while (!worklist.isEmpty()) {
        Stmt s = worklist.poll();
        if (deadCode.contains(s) || liveCode.contains(s)) {
            continue;
        }
        // -*- 2. `if` 分支不可达代码 -*-
        if (s instanceof If) {
            Value value = ConstantPropagation.evaluate(((If) s).getCondition(), constants.getInFact(s));
            liveCode.add(s);
            if (value.isConstant()) {
                boolean ifFlag = value.getConstant() != 0;
                for (Edge<Stmt> edge : cfg.getOutEdgesOf(s)) {
                    // 若是 `if` 语句为真，那么将真条件分支放入liveCode集合中，反之同理
                    if ((edge.getKind() == Edge.Kind.IF_TRUE && ifFlag)
                            || (edge.getKind() == Edge.Kind.IF_FALSE && !ifFlag)) {
                        worklist.add(edge.getTarget());
                    }
                }
            } else {
                worklist.addAll(cfg.getSuccsOf(s));
            }
        }
        // -*- 3. `switch` 分支不可达代码 -*-
        else if (s instanceof SwitchStmt switchStmt) {
            Value value = ConstantPropagation.evaluate(switchStmt.getVar(), constants.getInFact(s));
            liveCode.add(s);
            if (value.isConstant()) {
                int switchInt = value.getConstant();
                for (Edge<Stmt> edge : cfg.getOutEdgesOf(s)) {
                    if (edge.isSwitchCase() && edge.getCaseValue() == switchInt) {
                        worklist.add(edge.getTarget());
                    }
                }
                // 处理 default 分支
                if (!switchStmt.getCaseValues().contains(switchInt)) {
                    worklist.add(switchStmt.getDefaultTarget());
                }
            } else {
                worklist.addAll(cfg.getSuccsOf(s));
            }
        }
        // -*- 4. 无用赋值 -*-
        else if (s instanceof AssignStmt<?, ?> assignStmt) {
            System.out.println("liveVars: " + liveVars.getOutFact(s));
            // 如果右表达式没有副作用; 左表达式为变量, 且并非活跃变量
            if (hasNoSideEffect(assignStmt.getRValue()) && assignStmt.getLValue() instanceof Var var
                    && !liveVars.getOutFact(s).contains(var)) {
                deadCode.add(s);
            } else {
                liveCode.add(s);
            }
            worklist.addAll(cfg.getSuccsOf(s));
        } else {
            liveCode.add(s);
            worklist.addAll(cfg.getSuccsOf(s));
        }
    }
    // -*- 1. 控制流不可达代码 -*-
    // 没遍历到的一律认为不可达
    for (Stmt stmt : ir) {
        if (!liveCode.contains(stmt)) {
            deadCode.add(stmt);
        }
    }
    return deadCode;
}
```