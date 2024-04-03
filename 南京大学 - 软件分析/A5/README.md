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

## 作业目录

### XXX目录

ss

## 作业实现

### Solver.addReachable

```

```