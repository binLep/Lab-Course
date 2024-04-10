## 笔记部分

### AddEdge 算法

等同于非上下文敏感指针分析，[点击跳转](../A5/README.md#addedge-算法)

### AddReachable 算法

改动不大，只是加了上下文的修饰

$$
\begin{array}{l}
\textcolor{red}{\text{AddReachable}}(\colorbox{khaki}{$c:$}m)\\
\quad \textbf{if}\enspace \colorbox{khaki}{$c:$}m \notin RM\enspace\textbf{then}\\
\qquad \text{add}\ \colorbox{khaki}{$c:$}m\ \text{to}\ RM\\
\qquad S\enspace\cup=\  S_{m}\\
\qquad\textbf{foreach}\enspace\textcolor{blue}{\text{i: x = new T()}}\enspace\in\enspace S_m\enspace\textbf{do}\\
\qquad\quad\text{add}\enspace\langle \colorbox{khaki}{$c:$}x,\ \lbrace\colorbox{khaki}{$c:$} o_i \rbrace\rangle\enspace\text{to}\enspace WL\\
\qquad\textbf{foreach}\enspace\textcolor{blue}{\text{x = y}}\enspace\in\enspace S_m\enspace\textbf{do}\\
\qquad\quad \textcolor{red}{\text{AddEdge}}(\colorbox{khaki}{$c:$}y,\colorbox{khaki}{$c:$}x)
\end{array}
$$

### Propagate 算法

等同于非上下文敏感指针分析，[点击跳转](../A5/README.md#propagate-算法)

### ProcessCall 算法

$$
\begin{array}{l}
\textcolor{red}{\text{ProcessCall}}(\colorbox{khaki}{$c:$}x,\colorbox{khaki}{$c':$}o_i)\\
\quad\textbf{foreach}\enspace\textcolor{blue}{\text{l: r = x.k(a1,...an)}}\enspace\in\enspace S\enspace\textbf{do}\\
\qquad m = \textcolor{red}{\text{Dispatch}}(o_i,k)\\
\qquad\colorbox{khaki}{$c^t=\textcolor{red}{\text{Select}}(c,l,c':o_i,m)$}\\
\qquad\text{add}\enspace\langle\colorbox{khaki}{$c^t:$}m_{this},\lbrace\colorbox{khaki}{$c':$}o_i\rbrace\rangle\enspace to\enspace WL\\
\qquad\textbf{if}\enspace\colorbox{khaki}{$c:$}l\rightarrow\colorbox{khaki}{$c^t:$}m\ \notin\ CG\enspace\textbf{then}\\
\qquad\quad\text{add}\enspace\colorbox{khaki}{$c:$}l\rightarrow\colorbox{khaki}{$c^t:$}m\enspace to\enspace CG\\
\qquad\quad\textcolor{red}{\text{AddReachable}}(\colorbox{khaki}{$c^t:$}m)\\
\qquad\quad\textbf{foreach}\enspace parameter\ p_i\ of\ m\enspace\textbf{do}\\
\qquad\qquad\textcolor{red}{\text{AddEdge}}(\colorbox{khaki}{$c:$}a_i,\colorbox{khaki}{$c^t:$}p_i)\\
\qquad\quad\textcolor{red}{\text{AddEdge}}(\colorbox{khaki}{$c^t:$}m_{ret},\colorbox{khaki}{$c:$}r)\\
\end{array}
$$

这块上课内容给错了，上课给的是 $\text{AddReachable}(c:m)$，实际是 $\text{AddReachable}(c^t:m)$

### Select 算法

#### 元素信息

- $c$：caller context
- $l$：call site
- $c':o_i$：receiver object, with heap context
- $m$：callee（target method）

#### 实现方式

- k-call-site/k-CFA
  
  $\begin{array}{l}\textcolor{red}{\text{Select}}(\textcolor{red}{c},\textcolor{red}{l},c':o_i,m)=\lbrack\textcolor{red}{l_1,...l_k},l_{k+1}\rbrack\\ \qquad where\ c=\lbrack\textcolor{red}{l_0,...l_k}\rbrack\end{array}$

call-site 算法不能很好地处理：调用相同类中的函数但是不同实例的情况，因为此时被调用的函数不能作区分

- k-object
  
  $\begin{array}{l}\textcolor{red}{\text{Select}}(c,l,\textcolor{red}{c':o_i},m)=\lbrack\textcolor{red}{o_1,...o_k},o_{k+1}\rbrack\\ \qquad where\ c=\lbrack\textcolor{red}{o_0,...o_k}\rbrack\end{array}$

**理论上**，两种方法（CallSite & Object）不可比。而在针对 OO 语言（如Java）的**实践中**，object 方法的表现（无论是精度还是速度）**通常**更好，因为这更符合 OO 语言的特性

- k-type
  
  $\begin{array}{l}\textcolor{red}{\text{Select}}(c,l,\textcolor{red}{c':o_i},m)=\lbrack\textcolor{red}{t_1,...t_k},\textcolor{blue}{InType}(o_i)\rbrack\\ \qquad where\ c=\lbrack\textcolor{red}{t_0,...t_k}\rbrack\end{array}$

和 Object Sensitivity 类似，但是粒度更粗而效率更高 —— 这种方法只关注 Object 是在哪一个 Class 中被声明的

###  Solve 算法

$$
\begin{array}{l}
\textcolor{red}{\text{Solve}}(m^{entry})\\
\quad WL=\lbrack\enspace\rbrack,PFG=\lbrace\enspace\rbrace,S=\lbrace\enspace\rbrace,RM=\lbrace\enspace\rbrace,CG=\lbrace\enspace\rbrace\\
\quad\textcolor{red}{\text{AddReachable}}(\colorbox{khaki}{$\lbrack\ \rbrack:$}m^{entry})\\
\quad\textbf{while}\enspace WL\ \text{is not empty}\enspace\textbf{do}\\
\qquad\text{remove}\enspace\langle n.pts\rangle\enspace\text{from}\ WL\\
\qquad\textcolor{red}{\text{Propagate}}(n,pts)\\
\qquad\textbf{if}\enspace n\ \text{represents a variable \colorbox{khaki}{$c:$}x}\enspace\textbf{then}\\
\qquad\quad\textbf{foreach}\enspace\colorbox{khaki}{$c':$}o_i\in\Delta\enspace\textbf{do}\\
\qquad\qquad\textbf{foreach}\enspace\textcolor{blue}{\text{x.f = y}}\in\ S\enspace\textbf{do}\\
\qquad\qquad\quad\textcolor{red}{\text{AddEdge}}(\colorbox{khaki}{$c:$}y,\colorbox{khaki}{$c':$}o_i.f)\\
\qquad\qquad\textbf{foreach}\enspace\textcolor{blue}{\text{y = x.f}}\in\ S\enspace\textbf{do}\\
\qquad\qquad\quad\textcolor{red}{\text{AddEdge}}(\colorbox{khaki}{$c':$}o_i.f,\colorbox{khaki}{$c:$}y)\\
\qquad\qquad\textcolor{red}{\text{ProcessCall}}(\colorbox{khaki}{$c:$}x,\colorbox{khaki}{$c':$}o_i)\\
\end{array}
$$

## 作业目录

#### Solver 作业目录

| 函数                                                                           | 文件路径                        |
| :--------------------------------------------------------------------------- | :-------------------------- |
| [void addPFGEdge\(Pointer, Pointer\)](../A5/README.md#solveraddpfgedge)      | analysis/pta/cs/Solver.java |
| [void addReachable\(CSMethod\)](#solveraddreachable)                         | analysis/pta/cs/Solver.java |
| [PointsToSet propagate(Pointer, PointsToSet)](#solverpropagate)              | analysis/pta/cs/Solver.java |
| [void processCall\(Var, Obj\)](#solverprocesscall)                           | analysis/pta/cs/Solver.java |
| [class StmtProcessor implements StmtVisitor\<Void\>](#solverstmtprocessor)   | analysis/pta/cs/Solver.java |
| [\[\*\] void doProcessCall\(Context, Invoke, CSObj\)](#-solverdoprocesscall) | analysis/pta/cs/Solver.java |
| [void analyze\(\)](#solveranalyze)                                           | analysis/pta/cs/Solver.java |

### Select 作业目录

| 函数                                                                  | 文件路径                                               |
| :------------------------------------------------------------------ | :------------------------------------------------- |
| [class \_1CallSelector implements ContextSelector](#_1callselector) | analysis/pta/core/cs/selector/\_1CallSelector.java |
| [class \_2CallSelector implements ContextSelector](#_2callselector) | analysis/pta/core/cs/selector/\_2CallSelector.java |
| [class \_1ObjSelector implements ContextSelector](#_1objselector)   | analysis/pta/core/cs/selector/\_1ObjSelector.java  |
| [class \_2ObjSelector implements ContextSelector](#_2objselector)   | analysis/pta/core/cs/selector/\_2ObjSelector.java  |
| [class \_1TypeSelector implements ContextSelector](#_1typeselector) | analysis/pta/core/cs/selector/\_1TypeSelector.java |
| [class \_2TypeSelector implements ContextSelector](#_2typeselector) | analysis/pta/core/cs/selector/\_2TypeSelector.java |

## 作业实现

### Solver.addReachable

这里和 CI 不同，不再用单一的内部私有类变量 `stmtProcessor`，而是为每个 Stmt 创建独有的 `StmtProcessor` 实例类

```java
private void addReachable(CSMethod csMethod) {
    // TODO - finish me
    if (callGraph.contains(csMethod)) {
        return;
    }
    // S ∪= Sm
    callGraph.addReachableMethod(csMethod);
    // stmtProcessor
    for (Stmt stmt : csMethod.getMethod().getIR().getStmts()) {
        stmt.accept(new StmtProcessor(csMethod));
    }
}
```

### Solver.propagate

```java
private PointsToSet propagate(Pointer pointer, PointsToSet pointsToSet) {
    // TODO - finish me
    PointsToSet delta = PointsToSetFactory.make();
    PointsToSet ptN = pointer.getPointsToSet();
    // Δ = pts - pt(n)
    for (CSObj obj : pointsToSet) {
        if (!ptN.contains(obj)) {
            delta.addObject(obj);
        }
    }
    if (delta.isEmpty()) {
        return delta;
    }
    // pt(n) ∪= Δ
    for (CSObj obj : delta.getObjects()) {
        ptN.addObject(obj);
    }
    for (Pointer s : pointerFlowGraph.getSuccsOf(pointer)) {
        workList.addEntry(s, delta);
    }
    return delta;
}
```

### Solver.processCall

因为 StmtProcessor 的 Invoke 部分也要做差不多的处理，所以我创建了一个新的函数 `doProcessCall` 来压缩代码的体积

```java
private void processCall(CSVar recv, CSObj recvObj) {
    // TODO - finish me
    // No invoke is static here
    for (Invoke invoke : recv.getVar().getInvokes()) {
        doProcessCall(recv.getContext(), invoke, recvObj);
    }
}
```

### Solver.StmtProcessor

- 【STAGE1】处理 New 和 Assign 例子：实现 New、Copy 的类型重载
- 【STAGE2】处理 StaticField 例子：实现 Invoke、LoadField、StoreField 的类型重载

#### STAGE1

```java
public Void visit(New stmt) {
    Context heapContext = contextSelector.selectHeapContext(csMethod, heapModel.getObj(stmt));
    CSObj csObj = csManager.getCSObj(heapContext, heapModel.getObj(stmt));
    workList.addEntry(csManager.getCSVar(this.context, stmt.getLValue()), PointsToSetFactory.make(csObj));
    return null;
}

public Void visit(Copy stmt) {
    addPFGEdge(csManager.getCSVar(this.context, stmt.getRValue()), csManager.getCSVar(this.context, stmt.getLValue()));
    return null;
}
```

#### STAGE2

静态方法的 Invoke 是不需要实例化的，所以这里传入 null 值

```java
public Void visit(Invoke stmt) {
    if (!stmt.isStatic()) {
        return null;
    }
    doProcessCall(this.context, stmt, null);
    return null;
}

public Void visit(LoadField stmt) {
    if (!stmt.isStatic()) {
        return null;
    }
    JField jField = stmt.getFieldRef().resolve();
    addPFGEdge(csManager.getStaticField(jField), csManager.getCSVar(this.context, stmt.getLValue()));
    return null;
}

public Void visit(StoreField stmt) {
    if (!stmt.isStatic()) {
        return null;
    }
    JField jField = stmt.getFieldRef().resolve();
    addPFGEdge(csManager.getCSVar(this.context, stmt.getRValue()), csManager.getStaticField(jField));
    return null;
}
```

### \[\*\] Solver.doProcessCall

创建这个函数的本意是压缩 `StmtProcessor` 类和 `ProcessCall` 函数的代码量

相较于 `StmtProcessor` 类，`ProcessCall` 函数在处理的时候会多处理 this 指针

下面是函数的完整案例

```java
private void doProcessCall(Context c, Invoke l, CSObj o) {
    // m = Dispatch(o(i), k); 获取的是被调用的函数，也就是将要执行的函数，是存形参的函数
    JMethod m = resolveCallee(o, l);
    // c(t) = Select(c, l, c':o(i), m)
    CSCallSite csCallSite = csManager.getCSCallSite(c, l);
    // add <c(t):m(this), {c':o(i)}> to WL
    Context ct;
     if (!l.isStatic()) {
         ct = contextSelector.selectContext(csCallSite, o, m);
    } else {
         ct = contextSelector.selectContext(csCallSite, m);
    }
    CSMethod csTMethod = csManager.getCSMethod(ct, m);
    IR ir = csTMethod.getMethod().getIR();
    if (!l.isStatic()) {
        // add <c(t):m(this), {c':o(i)}> to WL
        workList.addEntry(csManager.getCSVar(ct, ir.getThis()), PointsToSetFactory.make(o));
    }
    // if c:l → c(t):m ∉ CG
    Edge<CSCallSite, CSMethod> edge = new Edge<>(CallGraphs.getCallKind(l), csCallSite, csTMethod);
    if (!callGraph.getInEdgesOf(csTMethod).contains(edge)) {
        callGraph.addEdge(edge);
        // AddReachable(c(t):m)
        addReachable(csManager.getCSMethod(ct, m));
        for (int i = 0; i < m.getParamCount(); i++) {
            // AddEdge(c:a(i), c(t):p(i))
            addPFGEdge(csManager.getCSVar(c, l.getInvokeExp().getArg(i)), csManager.getCSVar(ct, ir.getParam(i)));
        }
        // AddEdge(c(t):m(ret), c:r)
        if (l.getResult() != null) {
            // 主要 Java 也不会有两个返回值，爱咋写咋写吧
            for (Var retVar : ir.getReturnVars()) {
                addPFGEdge(csManager.getCSVar(ct, retVar), csManager.getCSVar(c, l.getResult()));
            }
        }
    }
}
```

### Solver.analyze

这部分改动不大，按公式写即可

```java
private void analyze() {
    // TODO - finish me
    while (!workList.isEmpty()) {
        WorkList.Entry entry = workList.pollEntry();
        Pointer ptN = entry.pointer();
        PointsToSet delta = propagate(ptN, entry.pointsToSet());
        if (ptN instanceof CSVar csVar) {
            Context ctx = csVar.getContext();
            for (CSObj recv : delta) {
                for (LoadField loadField : csVar.getVar().getLoadFields()) {
                    addPFGEdge(csManager.getInstanceField(recv, loadField.getFieldRef().resolve()), csManager.getCSVar(ctx, loadField.getLValue()));
                }
                for (StoreField storeField : csVar.getVar().getStoreFields()) {
                    addPFGEdge(csManager.getCSVar(ctx, storeField.getRValue()), csManager.getInstanceField(recv, storeField.getFieldRef().resolve()));
                }
                // LoadArray & StoreArray
                for (LoadArray loadArray : csVar.getVar().getLoadArrays()) {
                    addPFGEdge(csManager.getArrayIndex(recv), csManager.getCSVar(ctx, loadArray.getLValue()));
                }
                for (StoreArray storeArray : csVar.getVar().getStoreArrays()) {
                    addPFGEdge(csManager.getCSVar(ctx, storeArray.getRValue()), csManager.getArrayIndex(recv));
                }
                // 处理某变量x和x可能用到的的指针元素们
                processCall(csVar, recv);
            }
        }
    }
}
```

### \_1CallSelector

```java
public class _1CallSelector implements ContextSelector {

    @Override
    public Context getEmptyContext() {
        return ListContext.make();
    }

    @Override
    public Context selectContext(CSCallSite callSite, JMethod callee) {
        // TODO - finish me
        return ListContext.make(callSite.getCallSite());
    }

    @Override
    public Context selectContext(CSCallSite callSite, CSObj recv, JMethod callee) {
        // TODO - finish me
        return selectContext(callSite, callee);
    }

    @Override
    public Context selectHeapContext(CSMethod method, Obj obj) {
        // TODO - finish me
        return getEmptyContext();
    }
}
```

### \_2CallSelector

```java
public class _2CallSelector implements ContextSelector {

    @Override
    public Context getEmptyContext() {
        return ListContext.make();
    }

    @Override
    public Context selectContext(CSCallSite callSite, JMethod callee) {
        // TODO - finish me
        Context context = callSite.getContext();
        if (context.getLength() == 0) {
            return ListContext.make(callSite.getCallSite());
        } else {
            return ListContext.make(context.getElementAt(context.getLength() - 1), callSite.getCallSite());
        }
    }

    @Override
    public Context selectContext(CSCallSite callSite, CSObj recv, JMethod callee) {
        // TODO - finish me
        return selectContext(callSite, callee);
    }

    @Override
    public Context selectHeapContext(CSMethod method, Obj obj) {
        // TODO - finish me
        Context context = method.getContext();
        if (context.getLength() != 0) {
            return ListContext.make(context.getElementAt(context.getLength() - 1));
        }
        return context;
    }
}
```

### \_1ObjSelector

Object Sensitivity 算法注重传递 object 的上下文

虽然静态方法不存在 Obj 对象，但需要返回当前静态方法所属的上下文

```java
public class _1ObjSelector implements ContextSelector {

    @Override
    public Context getEmptyContext() {
        return ListContext.make();
    }

    @Override
    public Context selectContext(CSCallSite callSite, JMethod callee) {
        // TODO - finish me
        return callSite.getContext();
    }

    @Override
    public Context selectContext(CSCallSite callSite, CSObj recv, JMethod callee) {
        // TODO - finish me
        return ListContext.make(recv.getObject());
    }

    @Override
    public Context selectHeapContext(CSMethod method, Obj obj) {
        // TODO - finish me
        return getEmptyContext();
    }
}
```

### \_2ObjSelector

```java
public class _2ObjSelector implements ContextSelector {

    @Override
    public Context getEmptyContext() {
        return ListContext.make();
    }

    @Override
    public Context selectContext(CSCallSite callSite, JMethod callee) {
        // TODO - finish me
        return callSite.getContext();
    }

    @Override
    public Context selectContext(CSCallSite callSite, CSObj recv, JMethod callee) {
        // TODO - finish me
        Context context = recv.getContext();
        if (context.getLength() == 0) {
            return ListContext.make(recv.getObject());
        } else {
            return ListContext.make(context.getElementAt(context.getLength() - 1), recv.getObject());
        }
    }

    @Override
    public Context selectHeapContext(CSMethod method, Obj obj) {
        // TODO - finish me
        Context context = method.getContext();
        if (context.getLength() != 0) {
            return ListContext.make(context.getElementAt(context.getLength() - 1));
        }
        return context;
    }
}
```

### \_1TypeSelector

和 Object Sensitivity 不同的是，Type Sensitivity 返回的是创建对象时， Stmt 所处的类名

```java
public class _1TypeSelector implements ContextSelector {

    @Override
    public Context getEmptyContext() {
        return ListContext.make();
    }

    @Override
    public Context selectContext(CSCallSite callSite, JMethod callee) {
        // TODO - finish me
        return callSite.getContext();
    }

    @Override
    public Context selectContext(CSCallSite callSite, CSObj recv, JMethod callee) {
        // TODO - finish me
        return ListContext.make(recv.getObject().getContainerType());
    }

    @Override
    public Context selectHeapContext(CSMethod method, Obj obj) {
        // TODO - finish me
        return getEmptyContext();
    }
}
```

### \_2TypeSelector

注意对 `selectHeapContext` 函数的处理，此处也要获取上一个类型

```java
public class _2TypeSelector implements ContextSelector {

    @Override
    public Context getEmptyContext() {
        return ListContext.make();
    }

    @Override
    public Context selectContext(CSCallSite callSite, JMethod callee) {
        // TODO - finish me
        return callSite.getContext();
    }

    @Override
    public Context selectContext(CSCallSite callSite, CSObj recv, JMethod callee) {
        // TODO - finish me
        Context context = recv.getContext();
        if (context.getLength() == 0) {
            return ListContext.make(recv.getObject().getContainerType());
        } else {
            return ListContext.make(context.getElementAt(context.getLength() - 1), recv.getObject().getContainerType());
        }
    }

    @Override
    public Context selectHeapContext(CSMethod method, Obj obj) {
        // TODO - finish me
        Context context = method.getContext();
        if (context.getLength() == 0) {
            return getEmptyContext();
        } else {
            return ListContext.make(context.getElementAt(context.getLength() - 1));
        }
    }
}
```