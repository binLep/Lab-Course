## 作业目录

### 常量传播目录

因为我觉得维护代码最好干湿分离，就先不考虑改动常量传播的代码了，常量传播部分的代码就和以前一样就好

| 函数                                                                                                                | 文件路径                                                          |
| :---------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------ |
| [ConstantPropagation.newBoundaryFact(CFG\<Stmt\>)](../A2/README.md#constantpropagationnewboundaryfact)            | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [CPFact ConstantPropagation.newInitialFact()](../A2/README.md#constantpropagationnewinitialfact)                  | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [void ConstantPropagation.meetInto\(CPFact, CPFact\)](../A2/README.md#constantpropagationmeetinto)                | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [Value ConstantPropagation.meetValue\(Value, Value\)](../A2/README.md#constantpropagationmeetvalue)               | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [boolean ConstantPropagation.transferNode(Stmt, CPFact, CPFact)](../A2/README.md#constantpropagationtransfernode) | analysis/dataflow/analysis/constprop/ConstantPropagation.java |
| [Value ConstantPropagation.evaluate(Exp, CPFact)](../A2/README.md#constantpropagationevaluate)                    | analysis/dataflow/analysis/constprop/ConstantPropagation.java |

### 上下文敏感分析目录

指针分析的部分，此处只实现 2-obj 的算法就行，但我看国外的教程都是教的 1-type，不知道为啥

| 函数                                                                                          | 文件路径                                              |
| :------------------------------------------------------------------------------------------ | :------------------------------------------------ |
| [void addPFGEdge\(Pointer, Pointer\)](../A5/README.md#solveraddpfgedge)                     | analysis/pta/cs/Solver.java                       |
| [void addReachable\(CSMethod\)](../A6/README.md#solveraddreachable)                         | analysis/pta/cs/Solver.java                       |
| [PointsToSet propagate(Pointer, PointsToSet)](../A6/README.md#solverpropagate)              | analysis/pta/cs/Solver.java                       |
| [void processCall\(Var, Obj\)](../A6/README.md#solverprocesscall)                           | analysis/pta/cs/Solver.java                       |
| [class StmtProcessor implements StmtVisitor\<Void\>](../A6/README.md#solverstmtprocessor)   | analysis/pta/cs/Solver.java                       |
| [\[\*\] void doProcessCall\(Context, Invoke, CSObj\)](../A6/README.md#-solverdoprocesscall) | analysis/pta/cs/Solver.java                       |
| [void analyze\(\)](../A6/README.md#solveranalyze)                                           | analysis/pta/cs/Solver.java                       |
| [class \_2ObjSelector implements ContextSelector](../A6/README.md#_2objselector)            | analysis/pta/core/cs/selector/\_2ObjSelector.java |

### 过程间常量传播目录（本章）

InterSolver 类中的内容基本不变

这里需要修改 `InterSolver.java` 文件中的两个私有变量的限定修饰符，将其从 private 改为 default

```java
DataflowResult<Node, Fact> result;
Queue<Node> workList;
```

因为我们需要在进行过程间常量传播的过程中，调用 workList，并获取 result 中的 outFact 元素

| 函数                                                         | 文件路径                                     |
| :--------------------------------------------------------- | :--------------------------------------- |
| [void initialize()](../A4/README.md#intersolverinitialize) | analysis/dataflow/inter/InterSolver.java |
| [void doSolve()](../A4/README.md#intersolverdosolve)       | analysis/dataflow/inter/InterSolver.java |

InterConstantPropagation 类中有较大改动，也有部分函数照抄就行

| 函数                                                                                                                                    | 文件路径                                                  |
| :------------------------------------------------------------------------------------------------------------------------------------ | :---------------------------------------------------- |
| [CPFact transferNormalEdge(NormalEdge\<Stmt\>, CPFact)](../A4/README.md#interconstantpropagationtransfernormaledge)                   | analysis/dataflow/inter/InterConstantPropagation.java |
| [CPFact transferCallToReturnEdge(CallToReturnEdge\<Stmt\>, CPFact)](../A4/README.md#interconstantpropagationtransfercalltoreturnedge) | analysis/dataflow/inter/InterConstantPropagation.java |
| [CPFact transferCallEdge(CallEdge\<Stmt\>, CPFact)](../A4/README.md#interconstantpropagationtransfercalledge)                         | analysis/dataflow/inter/InterConstantPropagation.java |
| [CPFact transferReturnEdge(ReturnEdge\<Stmt\>, CPFact)](../A4/README.md#interconstantpropagationtransferreturnedge)                   | analysis/dataflow/inter/InterConstantPropagation.java |
| [boolean transferCallNode(Stmt, CPFact, CPFact)](../A4/README.md#interconstantpropagationtransfercallnode)                            | analysis/dataflow/inter/InterConstantPropagation.java |
| [【改】boolean transferNonCallNode(Stmt, CPFact, CPFact)](#interconstantpropagationtransfernoncallnode)                                  | analysis/dataflow/inter/InterConstantPropagation.java |
| [【新】void initialize\(\)](#interconstantpropagationinitialize)                                                                         | analysis/dataflow/inter/InterConstantPropagation.java |
| [【新】boolean transferStoreFieldNode\(StoreField, CPFact, CPFact\)](#interconstantpropagationtransferstorefieldnode)                    | analysis/dataflow/inter/InterConstantPropagation.java |
| [【新】boolean transferLoadFieldNode\(LoadField, CPFact, CPFact\)](#interconstantpropagationtransferloadfieldnode)                       | analysis/dataflow/inter/InterConstantPropagation.java |
| [【新】boolean transferStoreArrayNode\(StoreArray, CPFact, CPFact\)](#interconstantpropagationtransferstorearraynode)                    | analysis/dataflow/inter/InterConstantPropagation.java |
| [【新】boolean transferLoadArrayNode\(LoadArray, CPFact, CPFact\)](#interconstantpropagationtransferloadarraynode)                       | analysis/dataflow/inter/InterConstantPropagation.java |
| [【新】boolean isAliasArrayIndex\(Value, Value\)](#interconstantpropagationisaliasarrayindex)                                            | analysis/dataflow/inter/InterConstantPropagation.java |

## 作业实现

### InterConstantPropagation.initialize

```java
protected void initialize() {
    String ptaId = getOptions().getString("pta");
    PointerAnalysisResult pta = World.get().getResult(ptaId);
    // You can do initialization work here
    // 初始化具有堆抽象元素的实例化（非静态）指针，将变量和表达式关联
    // 设置每个变量对应的元素字典，二次循环寻找存在交集的变量，先假定他们的关系为别名，获取别名所对应的所有表达式，作为各自的公共集合
    // 这样可以处理当 x.f 作为别名变量 n 在程序执行时，对于 n 所处表达式，对于内部 Field 的更改
    // 注意：我们没有修改 PointsToSet 的内容，以下仅是绑定各种变量和表达式的操作，用于在遇到表达式的时候进行别名上的常量传播
    for (Var var : pta.getVars()) {
        this.instanceStoreFieldMap.putAll(var, var.getStoreFields());
        this.instanceLoadFieldMap.putAll(var, var.getLoadFields());
        this.storeArrayMap.putAll(var, var.getStoreArrays());
        this.loadArrayMap.putAll(var, var.getLoadArrays());
        for (Var v : pta.getVars()) {
            if (!var.equals(v) && pta.getPointsToSet(var).stream().anyMatch(o -> pta.getPointsToSet(v).contains(o))) {
                this.instanceStoreFieldMap.putAll(var, v.getStoreFields());
                this.instanceLoadFieldMap.putAll(var, v.getLoadFields());
                this.storeArrayMap.putAll(var, v.getStoreArrays());
                this.loadArrayMap.putAll(var, v.getLoadArrays());
            }
        }
    }
    // 初始化静态字段，将 Field 和表达式关联
    // 静态 Field 不用考虑指针集，所以只需要在后续不断地 meetValue 确认常量的 Value 就行
    for (JMethod jMethod : pta.getCallGraph().reachableMethods().toList()) {
        for (Stmt stmt : jMethod.getIR().getStmts()) {
            if (stmt instanceof StoreField storeField && storeField.isStatic()) {
                // 左值o(i)为 value，右值为 key
                this.staticStoreFieldMap.put(storeField.getFieldRef().resolve(), storeField);
            } else if (stmt instanceof LoadField loadField && loadField.isStatic()) {
                this.staticloadFieldMap.put(loadField.getFieldRef().resolve(), loadField);
            }
        }
    }
}
```

### InterConstantPropagation.transferNonCallNode

这部分改动较大，我们需要在这里处理别名分析

```java
protected boolean transferNonCallNode(Stmt stmt, CPFact in, CPFact out) {
    // TODO - finish me
    // 里面很多处理可以用 reduce 来解决，但是为了考虑在其他语言中实现该内容，在此不打算用高级函数处理
    if (stmt instanceof StoreField storeField) return transferStoreFieldNode(storeField, in, out);
    else if (stmt instanceof LoadField loadField) return transferLoadFieldNode(loadField, in, out);
    else if (stmt instanceof StoreArray storeArray) return transferStoreArrayNode(storeArray, in, out);
    else if (stmt instanceof LoadArray loadArray) return transferLoadArrayNode(loadArray, in, out);
    else return cp.transferNode(stmt, in, out);
}
```

### InterConstantPropagation.transferStoreFieldNode

```java
private boolean transferStoreFieldNode(StoreField storeField, CPFact in, CPFact out) {
    // Store 操作需要注意表达式的右值可能是一个二元表达式，但在 Tai-e 中没有这个顾虑
    // 若是此处进行了 StoreField 的更新，也就是说明对应的 LoadField 会存在更新的情况，此时就需要将其重新加入 WL 进行处理
    boolean changed = out.copyFrom(in);
    Var var = storeField.getRValue();
    if (!ConstantPropagation.canHoldInt(var) || !changed) return changed;
    List<LoadField> loadFieldList;
    if (storeField.isStatic()) {
        loadFieldList = this.staticloadFieldMap.get(storeField.getFieldAccess().getFieldRef().resolve()).stream().toList();
    } else {
        List<LoadField> baseList = this.instanceLoadFieldMap.get(((InstanceFieldAccess) storeField.getFieldAccess()).getBase()).stream().toList();
        loadFieldList = new ArrayList<>();
        for (LoadField loadField : baseList) {
            // 我们要匹配 x.f，但是 storeField 中可能存的是 x.h，所以要判断一下
            if (loadField.getFieldAccess().getFieldRef().resolve().equals(storeField.getFieldAccess().getFieldRef().resolve())) {
                loadFieldList.add(loadField);
            }
        }
    }
    this.solver.workList.addAll(loadFieldList);
    return true;
}
```

### InterConstantPropagation.transferLoadFieldNode

```java
private boolean transferLoadFieldNode(LoadField loadField, CPFact in, CPFact out) {
    // 要记得判断处理的变量是否为可存储 int 类型的变量
    Var var = loadField.getLValue();
    if (!ConstantPropagation.canHoldInt(var)) return out.copyFrom(in);
    // 这里我们遇到了 LoadField 表达式处理 Static 变量的情况，假设 Field 为 x.f
    // 我们去 staticStoreField 的字典里获取 x.f 的 value，也就是调用 x.f 的所有 StoreField 表达式
    // 通过获取在 StoreField 中存储的 x.f 在当前分析中存储的常量值，也就是各种 StoreField 下的 x.f 当前的最新值
    // 进行 meetValue 操作后得到 x.f 用于进行分析的最新值
    CPFact inFact = in.copy();
    List<StoreField> storeFieldList;
    if (loadField.isStatic()) {
        storeFieldList = this.staticStoreFieldMap.get(loadField.getFieldAccess().getFieldRef().resolve()).stream().toList();
    } else {
        // 对于实例化的 Field，我们要去获取其 Field 的基元素
        storeFieldList = new ArrayList<>();
        List<StoreField> baseList = this.instanceStoreFieldMap.get(((InstanceFieldAccess) loadField.getFieldAccess()).getBase()).stream().toList();
        for (StoreField storeField : baseList) {
            // 我们要匹配 x.f，但是 storeField 中可能存的是 x.h，所以要判断一下
            if (storeField.getFieldAccess().getFieldRef().resolve().equals(loadField.getFieldAccess().getFieldRef().resolve())) {
                storeFieldList.add(storeField);
            }
        }
    }
    if (!storeFieldList.isEmpty()) {
        Value value = this.solver.result.getOutFact(storeFieldList.get(0)).get(storeFieldList.get(0).getRValue());
        for (int i = 1; i < storeFieldList.size(); i++) {
            value = cp.meetValue(value, this.solver.result.getOutFact(storeFieldList.get(i)).get(storeFieldList.get(i).getRValue()));
        }
        // 更新 var = x.f 中 var 的 InFact 信息
        inFact.update(var, value);
    }
    // 更新 outFact 内容，没有元素更新则返回 false
    return out.copyFrom(inFact);
}
```

### InterConstantPropagation.transferStoreArrayNode

```java
private boolean transferStoreArrayNode(StoreArray storeArray, CPFact in, CPFact out) {
    // 这里只用给 var 赋值，不用考虑赋的值来源于谁，就算来源下标不一样也给它优化了
    boolean changed = out.copyFrom(in);
    Var var = storeArray.getRValue();
    if (!ConstantPropagation.canHoldInt(var) || !changed) return changed;

    List<LoadArray> loadArrayList = this.loadArrayMap.get(storeArray.getArrayAccess().getBase()).stream().toList();
    if (!loadArrayList.isEmpty()) {
        this.solver.workList.addAll(loadArrayList);
    }
    return true;
}
```

### InterConstantPropagation.transferLoadArrayNode

`isAliasArrayIndex` 用于判断是否应该处理该下标的数组

```java
private boolean transferLoadArrayNode(LoadArray loadArray, CPFact in, CPFact out) {
    Var var = loadArray.getLValue();
    if (!ConstantPropagation.canHoldInt(var)) return out.copyFrom(in);

    // 下标若是变量的话，会有可能换值，需要检查下标的 Value 经过传播后是否仍为常量
    CPFact inFact = in.copy();
    Var base = loadArray.getArrayAccess().getBase();
    List<StoreArray> storeArrayList = this.storeArrayMap.get(base).stream().toList();
    if (!storeArrayList.isEmpty()) {
        Var index = loadArray.getArrayAccess().getIndex();
        Value value = null;
        for (StoreArray storeArray : storeArrayList) {
            // 找到下标一样的 Store 集合，获取其存储的值，将其 meetValue 后检查是否仍为常量
            if (isAliasArrayIndex(in.get(index), this.solver.result.getOutFact(storeArray).get(storeArray.getArrayAccess().getIndex()))) {
                if (value == null) {
                    value = this.solver.result.getOutFact(storeArray).get(storeArray.getRValue());
                } else {
                    value = cp.meetValue(value, this.solver.result.getOutFact(storeArray).get(storeArray.getRValue()));
                }
            }
        }
        if (value != null) {
            inFact.update(var, value);
        }
    }
    return out.copyFrom(inFact);
}
```

### InterConstantPropagation.isAliasArrayIndex

```java
private boolean isAliasArrayIndex(Value loadArrayValue, Value storeArrayValue) {
    if (loadArrayValue.isConstant() && storeArrayValue.isConstant())
        return loadArrayValue.getConstant() == storeArrayValue.getConstant();
    else return !loadArrayValue.isUndef() && !storeArrayValue.isUndef();
}
```