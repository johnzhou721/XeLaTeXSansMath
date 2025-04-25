# License
This repo is licensed under CC BY-SA 4.0, with credits to @user691586 on TeX.StackExchange for coming up with this approach, even though I did some nontrivial stuff.
# XeLaTeXSansMath
Sans Serif Math in XeLaTeX / LuaLaTeX. Here's how you do it:
```latex
\documentclass{article}
\usepackage{tikz}
\usepackage{unicode-math}

\setmainfont[NFSSFamily=tnr]{Times New Roman}
\setsansfont[NFSSFamily=arial]{Arial}
\setmathfont[NFSSFamily=tgtermesmath]{TeX Gyre Termes Math}
\DeclareMathVersion{sans}
\setmathfont[version=sans,NFSSFamily=eulermath]{Euler Math}

\DeclareSymbolFont{myitfont}{TU}{eulermath}{m}{n}
\SetSymbolFont{myitfont}{sans}{TU}{arial}{m}{it}
\DeclareSymbolFont{myrmfont}{TU}{eulermath}{m}{n}
\SetSymbolFont{myrmfont}{sans}{TU}{arial}{m}{n}
\DeclareSymbolFont{mybffont}{TU}{eulermath}{m}{n}
\SetSymbolFont{mybffont}{sans}{TU}{arial}{b}{n}
\DeclareSymbolFont{mybfitfont}{TU}{eulermath}{m}{n}
\SetSymbolFont{mybfitfont}{sans}{TU}{arial}{b}{it}

\makeatletter
% For some reason I can not get the it shape if doing this globally
% Of course LaTeX very helpfully has made \DeclareMathSymbol preamble only.
\let\oriDeclareMathSymbol\DeclareMathSymbol
\def\makeletterssansinmath{\@ifstar{\@makeletterssansinmathnorm}{\@makeletterssansinmathfont}}
\def\@makeletterssansinmathnorm#1#2{
  % Sadly some effect of \DeclareMathSymbol appears to be global! Incredible!
  % By the way, would be easier with some expandable loop here.
  \def\restorelettersinmath{}%
  \@tfor\x:=abcdefghijklmnopqrstuvwxyz%
            ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+*=\do{%
      \edef\tmp{\global\Umathcodenum`\x=\noexpand\the\Umathcodenum`\x\relax}%
      \edef\restorelettersinmath{\restorelettersinmath\tmp}%
  }%
  \@tfor\x:=abcdefghijklmnopqrstuvwxyz%
            ABCDEFGHIJKLMNOPQRSTUVWXYZ\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathalpha}{#1}{`\x}}%
  }%
  \@tfor\x:=0123456789\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathalpha}{#2}{`\x}}%
  }%
  \@tfor\x:=+*\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathbin}{#2}{`\x}}%
  }%
  \@tfor\x:==\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathrel}{#2}{`\x}}%
  }%
}
\def\@makeletterssansinmathfont#1{
  % Sadly some effect of \DeclareMathSymbol appears to be global! Incredible!
  % By the way, would be easier with some expandable loop here.
  \def\restorelettersinmath{}%
  \@tfor\x:=abcdefghijklmnopqrstuvwxyz%
            ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+*=\do{%
      \edef\tmp{\global\Umathcodenum`\x=\noexpand\the\Umathcodenum`\x\relax}%
      \edef\restorelettersinmath{\restorelettersinmath\tmp}%
  }%
  \@tfor\x:=abcdefghijklmnopqrstuvwxyz%
            ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathalpha}{#1}{`\x}}%
  }%
  \@tfor\x:=+*\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathbin}{#1}{`\x}}%
  }%
  \@tfor\x:==\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathrel}{#1}{`\x}}%
  }%
}
\makeatother

\newenvironment{mysansmath}{%
\mathversion{sans}\makeletterssansinmath*{myitfont}{myrmfont}%
\def\symrm##1{{\makeletterssansinmath{myrmfont}##1\restorelettersinmath}}%
\def\symbf##1{{\makeletterssansinmath{mybffont}##1\restorelettersinmath}}%
\def\symbfit##1{{\makeletterssansinmath{mybfitfont}##1\restorelettersinmath}}%
\def\symit##1{{\makeletterssansinmath{myitfont}##1\restorelettersinmath}}%
\let\mathrm\symrm\let\mathbf\symbf\let\mathbfit\symbfit\let\mathit\symit%
}{\restorelettersinmath}

\newenvironment{mysansboldmath}{%
\mathversion{sans}\makeletterssansinmath*{mybfitfont}{mybffont}%
\def\symrm##1{{\makeletterssansinmath{mybffont}##1\restorelettersinmath}}%
\def\symbf##1{{\makeletterssansinmath{mybffont}##1\restorelettersinmath}}%
\def\symbfit##1{{\makeletterssansinmath{mybfitfont}##1\restorelettersinmath}}%
\def\symit##1{{\makeletterssansinmath{mybfitfont}##1\restorelettersinmath}}%
\let\mathrm\symrm\let\mathbf\symbf\let\mathbfit\symbfit\let\mathit\symit%
}{\restorelettersinmath}

%%begin novalidate
\BeforeBeginEnvironment{tikzpicture}{\sffamily\begin{mysansmath}}
\AfterEndEnvironment{tikzpicture}{\end{mysansmath}}
%%end novalidate


\begin{document}
\def\testdata{abc ABC $abc ABC \int_{z}^{Z}x^2\symbf d\, \symrm dx \symbfit{dx} \symit{something\ random\ 1434}+1278+\sqrt{1434}$}

\testdata

\begin{mysansmath}
    \[x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}\quad\int\]
\end{mysansmath}


\begin{tikzpicture}
\node {\testdata};
\end{tikzpicture}

\testdata

\begin{mysansboldmath}
    \[x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}\quad\int_0^1 x^2 + 1\]
\end{mysansboldmath}

%\showoutput
%\thispagestyle{empty}
\end{document}
```

Takes
```
abcdefghijklmnopqrstuvwxyz
ABCDEFGHIJKLMNOPQRSTUVWXYZ
0123456789+*=
```
from Arial. Rest of the symbols are from Euler Math.
