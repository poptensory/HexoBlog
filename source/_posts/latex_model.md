---
title: 美就是生产力——Latex排版
categories:
- 类别名称
tags:
- Latex
- 模板
toc: true
---
<Excerpt in index | > 
本文介绍一个简单又好看的Latex模板<!-- more -->
<The rest of contents | 余下全文>

Latex版本：CTEX_2.9.2.164.exe
考虑到一些包的依赖问题，不建议下载最新版本。

```Python
\documentclass[15pt,a4paper]{article}
\usepackage{CJK}
\usepackage{abstract}
\usepackage{indentfirst}    % 用于每个section的第一段缩进，否则不缩进
\usepackage{geometry}   % 用于下面设置左右上下的页边距
\newgeometry{left=2.5cm, right=2.5cm, top=1cm, bottom=2cm}  % 设置左右上下的页边距

\usepackage{algorithm, verbatim, algorithmic, graphicx} % 插入图片
\usepackage[tight,footnotesize]{subfigure}  % 子图

\usepackage{algorithm, verbatim, algorithmic, graphicx, threeparttable, multirow,
cite, amssymb, tabularx, blindtext}

\usepackage{caption}    % 修改Figure 为 图

\begin{CJK*}{GBK}{song}

\captionsetup[figure]{labelfont={bf},name={图},labelsep=period}  % 修改Figure 为 图，这句应该在CJK后面

% 定义公式大小
% \newenvironment{sequation}{\begin{equation}\large}{\end{equation}}
\newenvironment{sequation}{\begin{equation}}{\end{equation}}

\title{一份简单又好看的Latex模板}  % 这是文章的标题

\author{http://www.tensory.online}  % 作者

\date{2019/02/12} % 设置日期为空，即不设置。或者不写，自动设置日期。

\begin{document}

\maketitle

% 插入目录
% \tableofcontents

% 自定义设置摘要的形式
\renewcommand{\abstractname}{\begin{flushleft}\textbf{\Large{摘\ 要}}\end{flushleft}}

\begin{abstract}
\begin{flushleft}
本文给出一份简单又好看的Latex模板。
\end{flushleft}
\end{abstract}

\begin{flushleft}
\textbf{\Large{关键词：}}Latex；排版
\end{flushleft}

\section{引言}
Latex是非常好用的排版软件。

\section{量子概率论}

我们对QP理论和CP理论进行比较，假设这两个理论的样本空间或向量空间均为有限空间\cite{Zheng2013The}\cite{Griffiths2002Consistent}。

\subsection{QP理论与CP理论}

CP理论中的条件概率定义如下，如果事件$A$已知，那么$B$发生的概率为
\begin{sequation}
p_A(B)=p(B|A)=\frac{p(A\cap B)}{p(A)}.
\end{sequation}

\subsection{合取谬论与Linda问题}

不相容的事件可以用斜角的子空间表示，如图\ref{fig_quantum_cognition}所示。
\begin{figure}[h]
\centering
\includegraphics[width=4cm]{fig_quantum_cognition.png}
\caption{Linda问题的量子概率论解释}
\label{fig_quantum_cognition}
\end{figure}

\addcontentsline{toc}{chapter}{\protect\numberline{}{参考文献}}
%\bibliographystyle{ieeetr}
%\bibliography{enhance-ref}

\section{结论}
本文简要说明了量子认知的概念。

%把Latex中的 Reference 写成中文的"参考文献"
%%如果文档类是article之类的, 用\renewcommand\refname{参考文献}
%%如果文档类是book之类的, 用\renewcommand\bibname{参考文献}
\renewcommand\refname{参考文献}
\bibliographystyle{unsrt}%设置参考文献的类型 (bibliography style). 标准的为 plain
\bibliography{enhance-ref}%告诉LaTeX生成参考文献列表  enhance-ref.bib 是同目录下的参考文献管理文件
% \bibliographystyle{unsrt}

\end{CJK*}

\end{document}
```

参考文献文件enhance-ref.bib中的内容为
```
@article{Zheng2013The,
  title={The potential of using quantum theory to build models of cognition},
  author={Zheng, Wang and Busemeyer, Jerome R, and Harald, Atmanspacher and Pothos, Emmanuel M,},
  journal={Topics in Cognitive Science},
  volume={5},
  number={4},
  pages={672-688},
  year={2013},
}

@book{Griffiths2002Consistent,
  title={Consistent Quantum Theory},
  author={Griffiths, Robert B},
  year={2002},
}
```

效果：
![效果还行](https://raw.githubusercontent.com/poptensory/MarkdownPictures/master/show_latex.png "看看效果")