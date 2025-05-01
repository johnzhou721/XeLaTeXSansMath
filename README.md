# License
This repo is licensed under CC BY-SA 4.0, with credits to @user691586 on TeX.StackExchange for coming up with this approach, even though I did some nontrivial stuff.
# XeLaTeXSansMath
Sans Serif Math in XeLaTeX / LuaLaTeX. Here's how you do it:
```latex
\documentclass{article}
\usepackage{tikz}
\usepackage{unicode-math}

\setmainfont[NFSSFamily=palat]{Palatino}
\setsansfont[NFSSFamily=Candara]{candara.ttf}
[BoldFont=candara-bold.ttf, BoldItalicFont=candara-bold-italic.ttf, ItalicFont=candara-italic.ttf]
\setmathfont[NFSSFamily=tgtermesmath]{TeX Gyre Pagella Math}
\DeclareMathVersion{sans}
\setmathfont[version=sans,NFSSFamily=eulermath]{Euler Math}

\DeclareSymbolFont{myitfont}{TU}{eulermath}{m}{n}
\SetSymbolFont{myitfont}{sans}{TU}{Candara}{m}{it}
\DeclareSymbolFont{myrmfont}{TU}{eulermath}{m}{n}
\SetSymbolFont{myrmfont}{sans}{TU}{Candara}{m}{n}
\DeclareSymbolFont{mybffont}{TU}{eulermath}{m}{n}
\SetSymbolFont{mybffont}{sans}{TU}{Candara}{b}{n}
\DeclareSymbolFont{mybfitfont}{TU}{eulermath}{m}{n}
\SetSymbolFont{mybfitfont}{sans}{TU}{Candara}{b}{it}
\DeclareSymbolFont{sansops}{TU}{\sfdefault}{m}{n}
\SetSymbolFont{sansops}{bold}{TU}{\sfdefault}{b}{n}
\DeclareSymbolFont{bfsansops}{TU}{\sfdefault}{b}{n}
\SetSymbolFont{bfsansops}{bold}{TU}{\sfdefault}{b}{n}

\makeatletter
% For some reason I can not get the it shape if doing this globally
% Of course LaTeX very helpfully has made \DeclareMathSymbol preamble only.
\let\oriDeclareMathSymbol\DeclareMathSymbol
\def\makeletterssansinmath{\@ifstar{\@makeletterssansinmathnorm}{\@makeletterssansinmathfont}}
\def\@makeletterssansinmathnorm#1#2#3{
  % Sadly some effect of \DeclareMathSymbol appears to be global! Incredible!
  % By the way, would be easier with some expandable loop here.
  \def\restorelettersinmath{}%
  \@tfor\x:=abcdefghijklmnopqrstuvwxyz%
            ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+*=-×()\do{%
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
  \@tfor\x:=([\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathopen}{#2}{`\x}}%
  }%
  \@tfor\x:=)]\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathclose}{#2}{`\x}}%
  }%
  \@tfor\x:==\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathrel}{#2}{`\x}}%
  }%
  \renewcommand\operator@font{\expandafter\mathgroup\csname sym#3\endcsname}%
  \Umathcode`-="2 \csname sym#2\endcsname `−
  \Umathcode`×="2 \csname sym#2\endcsname `×
  \def\times{×}
}
\def\@makeletterssansinmathfont#1{
  % Sadly some effect of \DeclareMathSymbol appears to be global! Incredible!
  % By the way, would be easier with some expandable loop here.
  \def\restorelettersinmath{}%
  \@tfor\x:=abcdefghijklmnopqrstuvwxyz%
            ABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789+*=-×()[]\do{%
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
  \@tfor\x:=+*\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathbin}{#1}{`\x}}%
  }%
  \@tfor\x:=([\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathopen}{#1}{`\x}}%
  }%
  \@tfor\x:==\do{%
    \expanded{\noexpand\oriDeclareMathSymbol{\x}{\mathrel}{#1}{`\x}}%
  }%
  \Umathcode`-="2 \csname sym#1\endcsname `−
  \Umathcode`×="2 \csname sym#1\endcsname `×
  \def\times{×}
}
\makeatother

\newenvironment{mysansmath}{%
\mathversion{sans}\makeletterssansinmath*{myitfont}{myrmfont}{sansops}%
\let\mathrm\symrm\let\mathbf\symbf\let\mathbfit\symbfit\let\mathit\symit%
\def\symrm##1{{\makeletterssansinmath{myrmfont}##1\restorelettersinmath}}%
\def\symbf##1{{\makeletterssansinmath{mybffont}##1\restorelettersinmath}}%
\def\symbfit##1{{\makeletterssansinmath{mybfitfont}##1\restorelettersinmath}}%
\def\symit##1{{\makeletterssansinmath{myitfont}##1\restorelettersinmath}}%
}{\restorelettersinmath}

\newenvironment{mysansboldmath}{%
\mathversion{sans}\makeletterssansinmath*{mybfitfont}{mybffont}{bfsansops}%
\let\mathrm\symbf\let\mathbf\symbf\let\mathbfit\symbfit\let\mathit\symbfit%
\def\symrm##1{{\makeletterssansinmath{mybffont}##1\restorelettersinmath}}%
\def\symbf##1{{\makeletterssansinmath{mybffont}##1\restorelettersinmath}}%
\def\symbfit##1{{\makeletterssansinmath{mybfitfont}##1\restorelettersinmath}}%
\def\symit##1{{\makeletterssansinmath{mybfitfont}##1\restorelettersinmath}}%
}{\restorelettersinmath}

%%begin novalidate
\BeforeBeginEnvironment{tikzpicture}{\sffamily\begin{mysansmath}}
\AfterEndEnvironment{tikzpicture}{\end{mysansmath}}
%%end novalidate


\begin{document}


\def\testdata{
abc ABC $abc ABC \int_{z}^{Z}x^2\symbf d\, \symrm dx \symbfit{dx} \symit{something\ random\ 1434}+1278+\sqrt{1434}$
\[x=\frac{-b\pm\sqrt{b^2-4ac}}{2a}\quad\int_0^1 x^2 + 1 \Pr(A/B)\]
    \bfseries $(x+1)\times(x-1)=x\times x-1$ is a good formumla.
}

\def\testshortdata{
abc ABC $abc ABC \int_{z}^{Z}x^2\symbf d\, \symrm dx \symbfit{dx} \symit{something\ random\ 1434}+1278+\sqrt{1434}$
}

\testdata

\begin{tikzpicture}
    \node{\testshortdata};
\end{tikzpicture}

\begin{mysansmath}
    \testdata
\end{mysansmath}

\begin{mysansboldmath}
    \testdata
\end{mysansboldmath}

\testdata

$-$





%\showoutput
%\thispagestyle{empty}

\end{document}
```

Takes
```
abcdefghijklmnopqrstuvwxyz
ABCDEFGHIJKLMNOPQRSTUVWXYZ
0123456789+*×=
```
from Candara. Rest of the symbols are from Euler Math.

The code should be self-documenting. Hooks are inserted into tikzpicture to perform this automatically, since it's what most people wants.

