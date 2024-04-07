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
\qquad\quad\text{add}\enspace l\rightarrow m\enspace to\enspace CG\\
\qquad\quad\textcolor{red}{\text{AddReachable}}(\colorbox{khaki}{$c:$}m)\\
\qquad\quad\textbf{foreach}\enspace parameter\ p_i\ of\ m\enspace\textbf{do}\\
\qquad\qquad\textcolor{red}{\text{AddEdge}}(\colorbox{khaki}{$c:$}a_i,\colorbox{khaki}{$c^t:$}p_i)\\
\qquad\quad\textcolor{red}{\text{AddEdge}}(\colorbox{khaki}{$c^t:$}m_{ret},\colorbox{khaki}{$c:$}r)\\
\end{array}
$$

## 作业目录


## 作业实现

