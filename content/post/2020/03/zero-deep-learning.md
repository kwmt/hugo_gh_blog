+++
title= "ゼロから始めるDeepLearning"
date= 2020-03-06T12:00:00+09:00
draft = false
toc = true
slug = ""
author = "kwmt27"
categories = ["DeepLearning"]
tags = ["DeepLearning"]
keywords = [""]
twitterImage = "images/2019/12/android-research-and-presentation-3/arap.png"
+++
$$\begin{eqnarray}
    y = 
        \begin{cases} 
            0 & ( w_1 x_1 + w_2 x_2 \leqq \theta ) \\\\
            1 & ( w_1 x_1 + w_2 x_2 \gt \theta )
        \end{cases} \tag{1}
\end{eqnarray}$$


(1)の$\theta$を$-b$と置くと、

$$\begin{eqnarray}
    y = 
        \begin{cases} 
            0 & ( b + w_1 x_1 + w_2 x_2 \leqq 0 ) \\\\
            1 & ( b + w_1 x_1 + w_2 x_2 \gt 0 )
        \end{cases} \tag{2}
\end{eqnarray}$$

となり、$b$を**バイアス**と命名。
重みの$w_1$や$w_2$は入力信号への重要度をコントロールするパラメータとして機能。
バイアスは、発火のしやすさ（出力信号が1を出力度合い）を調整するパラメータとして機能。

たとえば、バイアス$b$が-0.1であれば、入力信号の重み付き和が0.1を上回るだけでニューロンが発火します。しかしもしbが-20であれば、入力信号の重み付き和が20を上回らなければニューロンは発火しません。


# test
$$F(x) = \sum_{n=1}^{N} \frac{1}{N}$$

$$\begin{eqnarray}
  x^n = \begin{cases}
    1 & (n=0) \\\\
    x \cdot x^{n-1} & (otherwise)
  \end{cases}
\end{eqnarray}$$

$$\begin{eqnarray}
    \max ( a, b ) = 
        \begin{cases} 
            a & ( a \geqq b ) \\\\
            b & ( a \lt b )
        \end{cases}
\end{eqnarray}$$

$$\LaTeX{}$$

