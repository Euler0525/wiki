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

% 链接颜色
\hypersetup{
    colorlinks=true,
    linkcolor=red,
    citecolor=green
}
```

## 页面大小

```latex
\geometry{a4paper,left=2.1cm,right=2.1cm,top=2.97cm,bottom=2.97cm}  % 页边距
\paperwidth 210mm
\paperheight 297mm
```

## 页眉

```latex
\pagestyle{fancy}
\fancyhead[L]{LEFT}
\fancyhead[C]{CENTER}
\fancyhead[R]{RIGHT}
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
```

## 代码块

```latex
\lstset{
    basicstyle = \fontsize{9}{9}\tt,
    keywordstyle = \bfseries
    numbers = left,                                   % 行号
    numberstyle=\tiny\color[RGB]{5,5,5},,             % 行号样式
    commentstyle=\it\color[RGB]{96,96,96},            % 注释样式
    frame = lrtb,                                     % 背景边框
    captionpos = t,
    showspaces = false
    showstringspaces = false,
    flexiblecolumns,
    rulesepcolor=\color{red!20!green!20!blue!20},
    escapeinside=``,
    xleftmargin=2em,xrightmargin=2em, aboveskip=1em,  
    framexleftmargin=1.5mm,
    backgroundcolor=\color[RGB]{250,250,250},         % 背景色
    keywordstyle=\color{blue}\bfseries,               % 关键字颜色
    identifierstyle=\bf,
    stringstyle=\rmfamily\slshape\color[RGB]{128,0,0},
    language=c++                                      % 语言
}

\begin{lstlisting}
    #include <iostream>
    using namespace std;

    int main()
    {
        cout << "Hello World!" << endl;
        return 0;
    }
\end{lstlisting}
```

## 公式

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

## 摘要

```latex
\newpage
\setcounter{page}{1}
\begin{abstract}
    摘要
    \noindent{\textbf{关键词：}关键词}
\end{abstract}
% 目录
\tableofcontents
```

