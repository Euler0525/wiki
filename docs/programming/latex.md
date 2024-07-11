# $\LaTeX$

```latex
\usepackage{url}
\usepackage{ctex}
\usepackage{tabu}
\usepackage{float}
\usepackage{amsthm}
\usepackage{amsmath}
\usepackage{xcolor}
\usepackage{array}
\usepackage{fancyhdr}
\usepackage{graphicx}
\usepackage{hyperref}
\usepackage{listings}
\usepackage{multirow}
\usepackage{tabularx}
\usepackage{longtable}
\usepackage{subcaption}
\usepackage{stackengine}
\usepackage{amsmath, amssymb}
\usepackage{titletoc, hyperref}
\usepackage[compress]{cite}
\usepackage[dvipdfm]{geometry}
```

### 最优化

$$
\begin{equation}
\begin{aligned} \label{P}
&\max_{\mathbf{w}, q}\quad \log_2{(1+\dfrac{|(\mathbf{h}_{IU}\mathbf{Q}\mathbf{H}_{AI}+\mathbf{h}_{AU})\mathbf{w}|^2}{\sigma_{U}^2})} - \log_2{(1+\dfrac{|(\mathbf{h}_{IE}\mathbf{Q}\mathbf{H}_{AI}+\mathbf{h}_{AE})\mathbf{w}|^2}{\sigma_{E}^2})}\\
&\begin{array}{r@{\quad}r@{}l@{\quad}l}
\text{s.t.} &||\mathbf{w}||^2\leq P_{AP}\\
	&|q_n|=1, \forall n\\
\end{array}
\end{aligned}
\end{equation}
$$

```latex
\begin{equation}
\begin{aligned} \label{P}
&\max_{\mathbf{w}, q}\quad \log_2{(1+\dfrac{|(\mathbf{h}_{IU}\mathbf{Q}\mathbf{H}_{AI}+\mathbf{h}_{AU})\mathbf{w}|^2}{\sigma_{U}^2})} - \log_2{(1+\dfrac{|(\mathbf{h}_{IE}\mathbf{Q}\mathbf{H}_{AI}+\mathbf{h}_{AE})\mathbf{w}|^2}{\sigma_{E}^2})}\\
&\begin{array}{r@{\quad}r@{}l@{\quad}l}
\text{s.t.} &||\mathbf{w}||^2\leq P_{AP}\\
	&|q_n|=1, \forall n\\
\end{array}
\end{aligned}
\end{equation}
```

## 表格

- 合并单元格
- 长表格自动分页
- 单元格内自动换行

```latex
\setlength{\tabcolsep}{2pt}           % 表格列间隔
\setlength\LTleft{-1in}               % 距页面左边界距离
\setlength\LTright{-1in plus 1 fill}  % 距页面右边界距离
{\small                               % 表格内字号
    \begin{longtable} {|c|c|p{5cm}|p{5cm}|l|}
        \caption{Title}
        \label{table:sheet}                                                                                                   \\
        \hline
        % 单页表头
        \multicolumn{2}{|c|}{\textbf{1}} & \multicolumn{1}{|c|}{\textbf{2}} & \multicolumn{1}{|c|}{\textbf{3}} & \textbf{4}   \\
        \hline
        \endfirsthead

        \hline
        % 分页后表头
        \multicolumn{2}{|c|}{\textbf{1}} & \multicolumn{1}{|c|}{\textbf{2}} & \multicolumn{1}{|c|}{\textbf{3}} & \textbf{4}   \\
        \hline
        \endhead

        \hline
        % 分页后划线
        \endfoot

        \hline
        \multirow{3}{*}{A}               & a                                &                                  &            & \\
        \cline{2-5}
                                         & b                                &                                  &            & \\
        \cline{2-5}
                                         & c                                &                                  &            & \\
        \hline
        \multirow{3}{*}{B}               &                                  &                                  &            & \\
        \cline{2-5}
                                         &                                  &                                  &            & \\
        \cline{2-5}
                                         &                                  &                                  &            & \\
        \hline
        \multicolumn{2}{|c|}{C}          &                                  &                                  &              \\
        \hline
    \end{longtable}
}
```

