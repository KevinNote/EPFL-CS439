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

