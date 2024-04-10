## 笔记部分

### AddEdge 算法

$$
\begin{array}{l}
\textcolor{red}{\text{AddEdge}}(s,t)\\
\quad\textbf{if}\enspace s\rightarrow t\enspace\notin\enspace PFG\\
\qquad\text{add}\enspace s\rightarrow t\enspace to\ PFG\\
\qquad\quad\textbf{if}\enspace pt(s)\ is\ not\ empty\enspace\textbf{then}\\
\qquad\qquad\text{add}\enspace\langle t,pt(s)\rangle\ to\ WL
\end{array}
$$

### AddReachable 算法

$$
\begin{array}{l}
\textcolor{red}{\text{AddReachable}}(m)\\
\quad \textbf{if}\enspace m \notin RM\enspace\textbf{then}\\
\qquad \text{add}\ m\ \text{to}\ RM\\
\qquad S\enspace\cup=\  S_{m}\\
\qquad\textbf{foreach}\enspace\textcolor{blue}{\text{i: x = new T()}}\enspace\in\enspace S_m\enspace\textbf{do}\\
\qquad\quad\text{add}\enspace\langle x,\ \lbrace o_i \rbrace\rangle\enspace\text{to}\enspace WL\\
\qquad\textbf{foreach}\enspace\textcolor{blue}{\text{x = y}}\enspace\in\enspace S_m\enspace\textbf{do}\\
\qquad\quad \textcolor{red}{\text{AddEdge}}(y,x)
\end{array}
$$

符号意义如下

$$
\begin{array}{l}
S&\text{Set of \textcolor{red}{reachable} statements}\\
S_m&\text{Set of \textcolor{red}{statements in method m}}\\
RM&\text{Set of \textcolor{red}{reachable methods}}\\
CG&\text{Call graph \textcolor{red}{edges}}
\end{array}
$$

### Propagate 算法

这里用差值传播

$$
\begin{array}{l}
\textcolor{red}{\text{Propagate}}(n,pts)\\
\quad\Delta\enspace=\enspace pts-pt(n)\\
\quad \textbf{if}\enspace \Delta\ \text{is\ not\ empty}\enspace\textbf{then}\\
\qquad pt(n)\enspace\cup=\enspace \Delta\\
\qquad\textbf{foreach}\enspace n\rightarrow s\enspace\in\enspace PFG\enspace\textbf{do}\\
\qquad\quad\text{add}\enspace\langle s,\Delta\rangle\enspace to\ WL
\end{array}
$$

需要注意此处的 $\Delta$，其具体意思如下

$$
\begin{equation}
\textbf{设：}\\
\begin{aligned}
pts=\lbrace o_1,o_3\rbrace\\
pt(x)=\lbrace o_1,o_2\rbrace\\
\end{aligned}
\qquad\textbf{那么：}\\
\begin{aligned}\\
\Delta&=pts-pt(x) \\
&=\lbrace o_1,o_3\rbrace-\lbrace o_1,o_2\rbrace\\
&=\lbrace o_3\rbrace\\
\end{aligned}
\end{equation}
$$

### ProcessCall 算法

$$
\begin{array}{l}
\textcolor{red}{\text{ProcessCall}}(x,o_i)\\
\quad\textbf{foreach}\enspace\textcolor{blue}{\text{l: r = x.k(a1,...an)}}\enspace\in\enspace S\enspace\textbf{do}\\
\qquad m = \textcolor{red}{\text{Dispatch}}(o_i,k)\\
\qquad\text{add}\enspace\langle m_{this},\lbrace o_i\rbrace\rangle\enspace to\enspace WL\\
\qquad\textbf{if}\enspace l\rightarrow m\ \notin\ CG\enspace\textbf{then}\\
\qquad\quad\text{add}\enspace l\rightarrow m\enspace to\enspace CG\\
\qquad\quad\textcolor{red}{\text{AddReachable}}(m)\\
\qquad\quad\textbf{foreach}\enspace parameter\ p_i\ of\ m\enspace\textbf{do}\\
\qquad\qquad\textcolor{red}{\text{AddEdge}}(a_i,p_i)\\
\qquad\quad\textcolor{red}{\text{AddEdge}}(m_{ret},r)\\
\end{array}
$$

###  Solve 算法

$$
\begin{array}{l}
\textcolor{red}{\text{Solve}}(m^{entry})\\
\quad WL=\lbrack\enspace\rbrack,PFG=\lbrace\enspace\rbrace,S=\lbrace\enspace\rbrace,RM=\lbrace\enspace\rbrace,CG=\lbrace\enspace\rbrace\\
\quad\textcolor{red}{\text{AddReachable}}(m^{entry})\\
\quad\textbf{while}\enspace WL\ \text{is not empty}\enspace\textbf{do}\\
\qquad\text{remove}\enspace\langle n.pts\rangle\enspace\text{from}\ WL\\
\qquad\textcolor{red}{\text{Propagate}}(n,pts)\\
\qquad\textbf{if}\enspace n\ \text{represents a variable x}\enspace\textbf{then}\\
\qquad\quad\textbf{foreach}\enspace o_i\in\Delta\enspace\textbf{do}\\
\qquad\qquad\textbf{foreach}\enspace\textcolor{blue}{\text{x.f = y}}\in\ S\enspace\textbf{do}\\
\qquad\qquad\quad\textcolor{red}{\text{AddEdge}}(y,o_i.f)\\
\qquad\qquad\textbf{foreach}\enspace\textcolor{blue}{\text{y = x.f}}\in\ S\enspace\textbf{do}\\
\qquad\qquad\quad\textcolor{red}{\text{AddEdge}}(o_i.f,y)\\
\qquad\qquad\textcolor{red}{\text{ProcessCall}}(x,o_i)\\
\end{array}
$$

处理（需要实例化的函数）指针的地方在 Propagate 函数后方

若处理的函数为静态函数，则在 AddReachable 函数调用的 StmtProcessor 内部类处理

## 作业目录

| 函数                                                                         | 文件路径                        |
| :------------------------------------------------------------------------- | :-------------------------- |
| [void addPFGEdge\(Pointer, Pointer\)](#solveraddpfgedge)                   | analysis/pta/ci/Solver.java |
| [void addReachable\(JMethod\)](#solveraddreachable)                        | analysis/pta/ci/Solver.java |
| [class StmtProcessor implements StmtVisitor\<Void\>](#solverstmtprocessor) | analysis/pta/ci/Solver.java |
| [PointsToSet propagate(Pointer, PointsToSet)](#solverpropagate)            | analysis/pta/ci/Solver.java |
| [void processCall\(Var, Obj\)](#solverprocesscall)                         | analysis/pta/ci/Solver.java |
| [void analyze\(\)](#solveranalyze)                                         | analysis/pta/ci/Solver.java |

## 作业实现

### Solver.addPFGEdge

上述算法的刻板实现，没有多余要说的

```java
private void addPFGEdge(Pointer source, Pointer target) {
    // TODO - finish me
    if (pointerFlowGraph.getSuccsOf(source).contains(target)) {
        return;
    }
    // add s → t to PFG
    pointerFlowGraph.addEdge(source, target);
    if (!source.getPointsToSet().isEmpty()) {
        // add <t, pt(s)> to WL
        workList.addEntry(target, source.getPointsToSet());
    }
}
```

### Solver.addReachable

和算法不一样的是，作业中该函数实际应用的算法，只截止于并操作部分

$$
\begin{array}{l}
\textcolor{red}{\text{AddReachable}}(m)\\
\quad \textbf{if}\enspace m \notin RM\enspace\textbf{then}\\
\qquad \text{add}\ m\ \text{to}\ RM\\
\qquad S\enspace\cup=\  S_{m}\\
\end{array}
$$

并且这里也没有明显表现 $S\enspace\cup=\  S_{m}$，因为我们完全可以依靠遍历 Method，再提取里面的 Stmt 来完成

至于后半部分的对不同表达式的处理，统一用交付给运用访问者模式思想的 stmtProcessor 类处理

```java
private void addReachable(JMethod method) {
    // TODO - finish me
    if (callGraph.contains(method)) {
        return;
    }
    // S ∪= Sm
    callGraph.addReachableMethod(method);
    // stmtProcessor
    for (Stmt stmt : method.getIR().getStmts()) {
        stmt.accept(stmtProcessor);
    }
}
```

### Solver.StmtProcessor

这是一个内部类，如下三个点对应了不同的 visit 处理函数（不同的重载类型）：

- 【STAGE1】处理 New 和 Copy 表达式：实现 New、Copy 的类型重载
- 【STAGE2】处理 Invoke 表达式中的 Static 函数：实现 Invoke 的类型重载
- 【STAGE3】处理 LoadField 和 StoreField 表达式中的 Static 函数：实现 LoadField 和 StoreField 的类型重载

#### STAGE1

```java
public Void visit(New stmt) {
    // 注意不要给 pt(s) 添加 o(i) 指针元素，这里只需将 o(i) 加入到 WL 即可
    workList.addEntry(pointerFlowGraph.getVarPtr(stmt.getLValue()), new PointsToSet(heapModel.getObj(stmt)));
    return null;
}

public Void visit(Copy stmt) {
    addPFGEdge(pointerFlowGraph.getVarPtr(stmt.getRValue()), pointerFlowGraph.getVarPtr(stmt.getLValue()));
    return null;
}
```

#### STAGE2

因为我们处理过程间指针传播的时候是依靠遍历堆抽象（指针）元素，并找到 Invoke 语句后进行处理的（见 analyze 和 processCall）

这样就无法处理无需实例化的静态函数了，静态函数调用是无法靠遍历堆抽象元素获取的

所以我们要在这个类中实现对于静态函数的指针元素（参数）传播

```java
public Void visit(Invoke stmt) {
    if(!stmt.isStatic()) {
        return null;
    }
    // 照抄 processCall，删掉处理 this 的部分就行
    // 被调用的函数，静态函数不用判断函数所在类，直接 null 就行
    JMethod jMethod = resolveCallee(null, stmt);
    IR ir = jMethod.getIR();
    // if l → m ∉ CG
    Edge<Invoke, JMethod> edge = new Edge<>(CallGraphs.getCallKind(stmt), stmt, jMethod);
    if(!callGraph.getInEdgesOf(jMethod).contains(edge)) {
        callGraph.addEdge(edge);
        // AddReachable(m)
        addReachable(jMethod);
        for(int i = 0; i < jMethod.getParamCount(); i++) {
            // AddEdge(a(i), p(i))
            addPFGEdge(pointerFlowGraph.getVarPtr(stmt.getInvokeExp().getArg(i)), pointerFlowGraph.getVarPtr(ir.getParam(i)));
        }
        // AddEdge(m(ret), r)
        if(stmt.getResult() != null) {
            // 主要 Java 也不会有两个返回值，爱咋写咋写吧
            for(Var retVar: ir.getReturnVars()) {
                addPFGEdge(pointerFlowGraph.getVarPtr(retVar), pointerFlowGraph.getVarPtr(stmt.getResult()));
            }
        }
    }
    return null;
}
```

#### STAGE3

和 analyze 函数中的处理相比，就是将函数 `getInstanceField` 替换成 `getStaticField`

```java
public Void visit(LoadField stmt) {
    if(!stmt.isStatic()) {
        return null;
    }
    JField jField = stmt.getFieldRef().resolve();
    addPFGEdge(pointerFlowGraph.getStaticField(jField), pointerFlowGraph.getVarPtr(stmt.getLValue()));
    return null;
}

public Void visit(StoreField stmt) {
    if(!stmt.isStatic()) {
        return null;
    }
    JField jField = stmt.getFieldRef().resolve();
    addPFGEdge(pointerFlowGraph.getVarPtr(stmt.getRValue()), pointerFlowGraph.getStaticField(jField));
    return null;
}
```

### Solver.propagate

标准函数模板，不用多说

```java
private PointsToSet propagate(Pointer pointer, PointsToSet pointsToSet) {
    // TODO - finish me
    PointsToSet delta = new PointsToSet();
    PointsToSet ptN = pointer.getPointsToSet();
    // Δ = pts - pt(n)
    for (Obj obj : pointsToSet) {
        if (!ptN.contains(obj)) {
            delta.addObject(obj);
        }
    }
    if (delta.isEmpty()) {
        return delta;
    }
    // pt(n) ∪= Δ
    for (Obj obj : delta.getObjects()) {
        ptN.addObject(obj);
    }
    for (Pointer s : pointerFlowGraph.getSuccsOf(pointer)) {
        workList.addEntry(s, delta);
    }
    return delta;
}
```

### Solver.processCall

注意一下函数的调用者语句若是没有赋值过程，是不用考虑给返回值添加一条边的

最让我奇怪的一点是：首先，算法里面有要求过滤已经存有的边，以防进行多余的添加操作，导致后续执行太多不必要代码的优化操作

但是实际情况中，要是使用本课程的 API 函数 hasEdge 的话，并不能通过 MergeParam 样例，这是因为同一个函数内在调用该函数时使用了不同的参数

本来我的代码如下所示

```java
if(!callGraph.hasEdge(var.getMethod(), jMethod)) {
    callGraph.addEdge(new Edge<>(CallGraphs.getCallKind(invoke), invoke, jMethod));
}
```

但这种情况会使得算法在执行 `foo(a2)` 时，匹配到已经有了 `foo(a1)`，从而不引入 a2 的指针，导致结果错误

深入发现，上述的 `hasEdge` 函数只会比较两条边连接的函数是否相等，但是并不能获取到调用点的内容（`a2` 和 `a1` 的差别），这就导致无法判断调用点是否相同，于是我改用了如下的判断方式

```java
if(!callGraph.getInEdgesOf(jMethod).contains(edge)) {}
```

最终代码如下

```java
private void processCall(Var var, Obj recv) {
    // TODO - finish me
    for(Invoke invoke: var.getInvokes()) {
        // m = Dispatch(o(i), k); 获取的是被调用的函数，也就是将要执行的函数，是存形参的函数
        JMethod jMethod = resolveCallee(recv, invoke);
        IR ir = jMethod.getIR();
        // add <m(this), {o(i)}> to WL
        workList.addEntry(pointerFlowGraph.getVarPtr(ir.getThis()), new PointsToSet(recv));
        // if l → m ∉ CG
        Edge<Invoke, JMethod> edge = new Edge<>(CallGraphs.getCallKind(invoke), invoke, jMethod);
        if(!callGraph.getInEdgesOf(jMethod).contains(edge)) {
            callGraph.addEdge(edge);
            // AddReachable(m)
            addReachable(jMethod);
            for(int i = 0; i < jMethod.getParamCount(); i++) {
                // AddEdge(a(i), p(i))
                addPFGEdge(pointerFlowGraph.getVarPtr(invoke.getInvokeExp().getArg(i)), pointerFlowGraph.getVarPtr(ir.getParam(i)));
            }
            // AddEdge(m(ret), r)
            if(invoke.getResult() != null) {
                // 主要 Java 也不会有两个返回值，爱咋写咋写吧
                for(Var retVar: ir.getReturnVars()) {
                    addPFGEdge(pointerFlowGraph.getVarPtr(retVar), pointerFlowGraph.getVarPtr(invoke.getResult()));
                }
            }
        }
    }
}
```

### Solver.analyze

只考虑 New 和 Assign，并且能进行过程间传播的话，代码如下所示

```java
private void analyze() {
    // TODO - finish me
    while (!workList.isEmpty()) {
        WorkList.Entry entry = workList.pollEntry();
        Pointer ptN = entry.pointer();
        PointsToSet delta = propagate(ptN, entry.pointsToSet());
        if (ptN instanceof VarPtr varPtr) {
            //!TODO: Load & Store
            for (Obj recv : delta) {
                // 处理某变量x和x可能用到的的指针元素们
                processCall(varPtr.getVar(), recv);
            }
        }
    }
}
```

考虑后续情况 StoreField、LoadField、StoreArray、LoadArray 后实现如下（静态处理在 StmtProcessor 中实现）

```java
private void analyze() {
    // TODO - finish me
    while (!workList.isEmpty()) {
        WorkList.Entry entry = workList.pollEntry();
        Pointer ptN = entry.pointer();
        PointsToSet delta = propagate(ptN, entry.pointsToSet());
        if (ptN instanceof VarPtr varPtr) {
            for (Obj recv : delta) {
                // LoadField & StoreField
                for (LoadField loadField: varPtr.getVar().getLoadFields()) {
                    addPFGEdge(pointerFlowGraph.getInstanceField(recv, loadField.getFieldRef().resolve()), pointerFlowGraph.getVarPtr(loadField.getLValue()));
                }
                for (StoreField storeField: varPtr.getVar().getStoreFields()) {
                    addPFGEdge(pointerFlowGraph.getVarPtr(storeField.getRValue()), pointerFlowGraph.getInstanceField(recv, storeField.getFieldRef().resolve()));
                }
                // LoadArray & StoreArray
                for (LoadArray loadArray: varPtr.getVar().getLoadArrays()) {
                    addPFGEdge(pointerFlowGraph.getArrayIndex(recv), pointerFlowGraph.getVarPtr(loadArray.getLValue()));
                }
                for (StoreArray storeArray: varPtr.getVar().getStoreArrays()) {
                    addPFGEdge(pointerFlowGraph.getVarPtr(storeArray.getRValue()), pointerFlowGraph.getArrayIndex(recv));
                }
                // 处理某变量x和x可能用到的的指针元素们
                processCall(varPtr.getVar(), recv);
            }
        }
    }
}
```