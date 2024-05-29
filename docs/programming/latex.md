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

## 希腊字母表

|     音标     | 希腊字母大写 | 希腊字母小写  | LaTeX 大写  |   LaTeX 小写   |
| :----------: | :----------: | :-----------: | :--------: | :-----------: |
|   /ˈælfə/    |   $\Alpha$   |   $\alpha$    |  `\Alpha`  |   `\alpha`    |
|   /ˈbeɪtə/   |   $\Beta$    |    $\beta$    |  `\Beta`   |    `\beta`    |
|   /ˈɡæmə/    |   $\Gamma$   |   $\gamma$    |  `\Gamma`  |   `\gamma`    |
|   /ˈdeltə/   |   $\Delta$   |   $\delta$    |  `\Delta`  |   `\delta`    |
|  /ˈepsɪlɒn/  |  $\Epsilon$  |  $\epsilon$   | `\Epsilon` |  `\epsilon`   |
|  /ˈepsɪlɒn/  |  $\Epsilon$  | $\varepsilon$ | `\Epsilon` | `\varepsilon` |
|   /ˈzeɪtə/   |   $\Zeta$    |    $\zeta$    |  `\Zeta`   |    `\zeta`    |
|   /ˈiːtə/    |    $\Eta$    |    $\eta$     |   `\Eta`   |    `\eta`     |
|   /ˈθiːtə/   |   $\Theta$   |   $\theta$    |  `\Theta`  |   `\theta`    |
|   /ˈθiːtə/   |   $\Theta$   |  $\vartheta$  |  `\Theta`  |  `\vartheta`  |
|  /aɪˈəʊtə/   |   $\Iota$    |    $\iota$    |  `\Iota`   |    `\iota`    |
|   /ˈkæpə/    |   $\Kappa$   |   $\kappa$    |  `\Kappa`  |   `\kappa`    |
|   /ˈkæpə/    |   $\Kappa$   |  $\varkappa$  |  `\Kappa`  |  `\varkappa`  |
|   /ˈlæmdə/   |  $\Lambda$   |   $\lambda$   | `\Lambda`  |   `\lambda`   |
|    /mjuː/    |    $\Mu$     |     $\mu$     |   `\Mu`    |     `\mu`     |
|    /njuː/    |    $\Nu$     |     $\nu$     |   `\Nu`    |     `\nu`     |
|    /zaɪ/     |    $\Xi$     |     $\xi$     |   `\Xi`    |     `\xi`     |
| /əʊˈmaɪkrɒn/ |  $\Omicron$  |  $\omicron$   | `\Omicron` |  `\omicron`   |
|    /paɪ/     |    $\Pi$     |     $\pi$     |   `\Pi`    |     `\pi`     |
|    /paɪ/     |    $\Pi$     |   $\varpi$    |   `\Pi`    |   `\varpi`    |
|    /rəʊ/     |    $\Rho$    |    $\rho$     |   `\Rho`   |    `\rho`     |
|    /rəʊ/     |    $\Rho$    |   $\varrho$   |   `\Rho`   |   `\varrho`   |
|   /ˈsɪɡmə/   |   $\Sigma$   |   $\sigma$    |  `\Sigma`  |   `\sigma`    |
|   /ˈsɪɡmə/   |   $\Sigma$   |  $\varsigma$  |  `\Sigma`  |  `\varsigma`  |
|    /tɔː/     |    $\Tau$    |    $\tau$     |   `\Tau`   |    `\tau`     |
| /ˈjuːpsɪlɒn/ |  $\Upsilon$  |  $\upsilon$   | `\Upsilon` |  `\upsilon`   |
|    /faɪ/     |    $\Phi$    |    $\phi$     |   `\Phi`   |    `\phi`     |
|    /faɪ/     |    $\Phi$    |   $\varphi$   |   `\Phi`   |   `\varphi`   |
|    /kaɪ/     |    $\Chi$    |    $\chi$     |   `\Chi`   |    `\chi`     |
|    /psaɪ/    |    $\Psi$    |    $\psi$     |   `\Psi`   |    `\psi`     |
|  /ˈəʊmɪɡə/   |   $\Omega$   |   $\omega$    |  `\Omega`  |   `\omega`    |

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

## 插图

论文图 ⽚ 的基本格式要求如下：

- 比例 $16:9$ 或 $4:3$

- 字号 $12pt$

- 字体与正 ⽂⼀ 致，⽂ 本 ⼀ 般为衬线字体 **Times New Roman** 公式 ⼀ 般为 **Latin Modern Math**

若用 `Python` 绘图可以加入如下全局配置：

```python
import matplotlib.pyplot as plt
# IEEETran 中 \textwidth 为 7. 1413 英⼨
# - 使⽤ `\printinunitsof{in}\prntlen{\textwidth}`
# - 可在⽣成的⽂件中打印输出⽂本的 \textwidth，需要使⽤到
# - `printlen` 和 `layouts` 两个包
textwidth_inches = 7.1413

# 设置图⽚宽度为 0.5 倍 \textwidth，并且设定图窗⽐例（16:9 或 4:3）
aspect = 16/9 # 或4/3
plot_leftpad, plot_rightpad, plot_bottompad, plot_toppad = 0.1, 0.95, 0.18, 0.95
plot_width = plot_rightpad - plot_leftpad
plot_hight = plot_toppad - plot_bottompad
fig_width = 0.5 * textwidth_inches
fig_hight = (fig_width * plot_width / aspect) / plot_hight

# 设置全局参数
plt.rcParams.update({
    "text.usetex": True,
    'text.latex.preamble': r'\usepackage{amsmath,amsfonts}',
    "font.family": "serif",
    "figure.figsize": (fig_width, fig_hight),
    "font.size": 8,
    "lines.linewidth": 0.5,
    "lines.markersize": 3,
    "figure.subplot.bottom": plot_bottompad,
    "figure.subplot.left": plot_leftpad,
    "figure.subplot.right": plot_rightpad,
    "figure.subplot.top": plot_toppad,
    "legend.markerscale": 1.0,
    "legend.fontsize": 7,
    "legend.borderpad": 0.2,
    "legend.handlelength": 1.5,
    "legend.handletextpad": 0.25,
})
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
 &|q_n|= 1, \forall n\\
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

