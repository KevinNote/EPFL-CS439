# Chapter 2: Gradient Descent

我们期望找到 $f(\mathbf{x}) - f(\mathbf{x}^*) < \varepsilon$

这里并不尝试使的 $\mathbf{x}$ 接近 $\mathbf{x}^*$，因为可逆存在很多minima

GD 通过 $T$ 次迭代使的 $f(\mathbf{x}_T) - f(\mathbf{x}^*) < \varepsilon$ 。

**Convergence Rate**: $\varepsilon_t = f(\mathbf{x}_t) - f(\mathbf{x})$
 Linear Convergence：存在一个实数 $c\in (0,1)$ 使得对于所有大的 $t$:
$$
\varepsilon _{t+1} \leq c \varepsilon_t
$$
其隐释了大多数 $\varepsilon$ 可以在 $\mathcal{O}(\log(1/\varepsilon))$ 次迭代收敛。

Superlinear Convergence: 存在一个实数 $c>0, r>1$ 使得对于所有大的 $t$:
$$
\varepsilon_{t+1} \leq c(\varepsilon_t)^r
$$
$r=2$ 则被称呼为 Quadratic Convergency，其收敛速度为 $\mathcal{O}(\log \log(1/\varepsilon))$

如果算法未达到 Linear Convergence，我们称呼起为 Sublinear Convergence。



## Algorithm

$$
\mathbf{x}_{t+1} = \mathbf{x}_t + \mathbf{v}_t
$$

我们通过 $f$ 位于点 $\mathbf{x}_t$ 的导数求解，可知：
$$
f(\mathbf{x}_t+\mathbf{v}_t) = f(\mathbf{x}_t) + \nabla f(\mathbf{x}_t)^\top\mathbf{v}_t + \underbrace {r(\mathbf{v}_t)}_{o(||\mathbf{v}_t||), ||\mathbf{v}_t||\sim0} \approx f(\mathbf{x}_t) + \nabla f(\mathbf{x}_t)^\top\mathbf{v}_t
$$
为了最小化，我们需要让 $\nabla f(\mathbf{x}_t)^\top\mathbf{v}_t<0$, 即：
$$
\mathbf{x}_{t+1} := \mathbf{x}_t - \eta \nabla f(\mathbf{x}_t)
$$

### Vanilla Analysis

我们将 $\mathbf{x}_t$ 与 $\mathbf{x}^*$ 的性能进行对比：
$$
f(\mathbf{x}_t) - f(\mathbf{x}^*) \leq \nabla f(\mathbf{x}_t )^\top(\mathbf{x}_t - \mathbf{x}^*)
$$
令 $g_t := \nabla f(\mathbf{x}_t) = (\mathbf{x}_t - \mathbf{x}_{t+1}) / \eta$

因此原式可用改写为
$$
\begin{align*}
f(\mathbf{x}_t) - f(\mathbf{x}^*)
&\leq \nabla f(\mathbf{x}_t )^\top(\mathbf{x}_t - \mathbf{x}^*)
\\
&=
\frac{1}{\eta}(\mathbf{x}_t - \mathbf{x}_{t+1})^\top(\mathbf{x}_t - \mathbf{x}^*)
\\
&= g_t^\top (\mathbf{x}_t - \mathbf{x}^*)
\\
&= \Gamma_t

\end{align*}
$$
令右式为 $\Gamma_t$, 考虑如下：
$$
2\mathbf{v}^\top\mathbf{w} = ||\mathbf{v}||^2  + ||\mathbf{w}||^2 - ||\mathbf{v} - \mathbf{w}||^2
$$
有
$$
\begin{align*}
\Gamma_t
&= \frac{1}{\eta}\underbrace{(\mathbf{x}_t - \mathbf{x}_{t+1})^\top}_{\mathbf{v}}\underbrace{(\mathbf{x}_t - \mathbf{x}^*)}_{\mathbf{w}}
\\
&= \frac{1}{2\eta}\left(
|| \mathbf{x}_t - \mathbf{x}_{t+1} ||^2 +
|| \mathbf{x}_t - \mathbf{x}^* ||^2 -
|| \mathbf{x}^* - \mathbf{x}_{t+1}||^2
\right)
\\
&= \frac{1}{2\eta}\left(
\eta^2|| g_t ||^2 +
|| \mathbf{x}_t - \mathbf{x}^* ||^2 -
|| \mathbf{x}_{t+1} - \mathbf{x}^*||^2
\right)
\\
&=
\frac{\eta}{2}|| g_t ||^2 +

\frac{1}{2\eta}\left(
|| \mathbf{x}_t - \mathbf{x}^* ||^2 -
|| \mathbf{x}_{t+1} - \mathbf{x}^*||^2
\right)
\end{align*}
$$

求和后会发现后侧式被消除（telescoping sum）
$$
\begin{align*}
\sum^{T-1}_{t=0} \Gamma_t &= 
\frac{\eta}{2}\sum^{T-1}_{t=0}|| g_t ||^2 +

\frac{1}{2\eta}\left(
|| \mathbf{x}_0 - \mathbf{x}^* ||^2 -
|| \mathbf{x}_{T} - \mathbf{x}^*||^2
\right)
\\
& \leq 
\frac{\eta}{2}\sum^{T-1}_{t=0}|| g_t ||^2 +

\frac{1}{2\eta}
|| \mathbf{x}_0 - \mathbf{x}^* ||^2
\end{align*}
$$

考虑：
$$
f(\mathbf{x}_t) - f(\mathbf{x}^*) \leq \Gamma_t\\
\sum^{T-1}_{t=0} 
\left [ f(\mathbf{x}_t) - f(\mathbf{x}^*) \right]
\leq 
\frac{\eta}{2}\sum^{T-1}_{t=0}|| g_t ||^2 +
\frac{1}{2\eta}
|| \mathbf{x}_0 - \mathbf{x}^* ||^2
$$
这给出了平均error的上界

这个结果好吗？一般来说，答案是否定的。对 $|| \mathbf{x}_0 - \mathbf{x}^* ||$ 的依赖是意料之中的（从 $\mathbf{x}^*$ 开始的距离越远，所需的时间就越长）；而对平方根 $|| g_t ||^2$ 的依赖则是更大的问题，如果我们无法控制它们，就不能说太多。

### Lipscheitz Convex Functions: $\mathcal{O}(1/\varepsilon^2)$ steps

> 令函数 $f: \mathbf{dom}(f) \to \mathbb{R}^m$ 是可导的，$X \subseteq \mathbf{dom}(f)$ 是一个convex set，$B\in \mathbb{R}^+$。如果 $X$ 是非空且是 open的，则下列两个陈述时等价的：
>
> 1. $f$ 是 B-Lipschitz 意味着 $||f(\mathbf{x_1}) - f(\mathbf{x_2})|| \leq B ||\mathbf{x_1} - \mathbf{x_2}||, \forall \mathbf{x_1}, \mathbf{x_2} \in X$
> 2. $f$ 是可导的且被bounded by $B$ (in spectral norm),意味着 $||Df(\mathbf{x})||\leq B, \forall \mathbf{x} \in X$

假设 $|| \mathbf{x}_0 - \mathbf{x}^* || \leq R, ||\nabla f(\mathbf{x})|| \leq B$，选择stepsize 为
$$
\eta := \frac{R}{B\sqrt{T}}
$$
因此有 
$$
\begin{align*}
\frac{1}{T}\sum^{T-1}_{t=0}
(f(\mathbf{x}_t) - f(\mathbf{x}^*))
&\leq
\frac{1}{T}\sum^{T-1}_{t=0} || \mathbf{x}_0 - \mathbf{x}^* ||\nabla f(\mathbf{x})
\\
&=\frac{1}{T}\sum^{T-1}_{t=0} RB
\\
&\leq \frac{RB}{\sqrt{T}}
\end{align*}
$$

> Pf (2):
> $$
> \begin{align*}
> \sum^{T-1}_{t=0}
> \left [ f(\mathbf{x}_t) - f(\mathbf{x}^*) \right]
> &\leq 
> \frac{\eta}{2}\sum^{T-1}_{t=0}|| g_t ||^2 +
> \frac{1}{2\eta}
> || \mathbf{x}_0 - \mathbf{x}^* ||^2
> 
> \\
> &\leq \frac{\eta}{2}B^2T + \frac{1}{2\eta} R^2
> \end{align*}
> $$
> 因此选择 $\eta$ 使的右式最小，即：
> $$
> q(\eta) = \frac{\eta}{2}B^2T + \frac{1}{2\eta} R^2
> $$
> 求导，可得
> $$
> q(R/(B\sqrt{T})) = RB\sqrt{T}
> $$
> QED

为使得 $\min^{T-1}_{t=0} \left [ f(\mathbf{x}_t) - f(\mathbf{x}^*) \right] \leq \varepsilon$, 需要
$$
T \geq \frac{R^2 B^2}{\varepsilon^2}
$$
次迭代。这个结果并不好，考虑误差要求很低，但是 R 和 B 可能是很大的数。

需要注意这里并没有对维度的依赖（尽管 R B 可能会有）。

如果我们不知道 R 和/或 B，该怎么办？一种方法是 “猜测 ”R 和 B，用猜测得到的 $T$ 和 $\eta$ 运行梯度下降，检查结果的绝对误差是否最大为 $\varepsilon$，否则换一个猜测重复。然而，这样做是失败的，因为为了计算绝对误差，我们需要知道 $f(\mathbf{x}^*)$，而我们通常不知道 $f(\mathbf{x}^*)$。但是练习 16 要求你证明知道 R 就足够了。



### Smooth Convex Functions: $\mathcal{O}(1/\varepsilon)$ steps

考虑一阶导性质：
$$
f(\mathbf{x}_2) \geq f(\mathbf{x}_1) + \nabla f(\mathbf{x}_1)^\top(\mathbf{x}_2 - \mathbf{x}_1)
$$
我们可以认为 $f(\mathbf{x}_2)$ be bounded from above. 因此对于所有可导函数，有：

如果区间 $X\subset \mathbf{dom}(f)$ 是convex 且 $L \in \mathbb{R}^+$,  $f$ 是 smooth (with param $L$) over $X$ 如果
$$
f(\mathbf{y}) \geq f(\mathbf{x}) + \nabla f(\mathbf{x}) ^\top (\mathbf{y}-\mathbf{x}) + \frac{L}{2} || \mathbf{x} - \mathbf{y}||^2, \quad \forall\mathbf{x}, \mathbf{y} \in X
$$
如果 $X = \mathbf{dom}(f)$, $f$ 被称呼为 smooth

<img src="./img/Chapter 2/image-20241219212745015.png" alt="image-20241219212745015" style="width:50%;" />

