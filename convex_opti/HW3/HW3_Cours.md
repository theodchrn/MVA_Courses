## Question 1


### Rappels HW2 
On se souvient que le dual de 
$$ \min_x \|Ax-b\|_2^2 + \|x\|_1 \quad \text{(P)}$$
a pour dual (en utilisant la norme duale $\|.\|_1^*$) 
$$\max_\nu -\dfrac{1}{4}\|\nu\|_2^2 + \nu^T b \quad \text{s.c.} \begin{cases} \|A^T\nu\|_\infty \leq 1 \\ \nu \geq 0 \end{cases} \quad\quad \text{(D)} $$
On va réutiliser le même raisonnement ici.
### Dans notre cas 
On réécrit notre problème :
$$ \begin{aligned} &\min_w\dfrac{1}{2} \|Xw-y\|_2^2 + \lambda\|w\|_1 \quad \text{(LASSO)}\\ \Leftrightarrow 
& \min_{z,w}\dfrac{1}{2} \|z\|_2^2 + \lambda\|w\|_1 \quad\quad \text{s.c.}~z-Xw+y=0\end{aligned}$$
Par similitude avec \(P\).

On a le lagrangien :
$$\begin{aligned}
\mathcal{L}(z,w,v)&= \dfrac{1}{2} \|z\|_2^2 + \lambda\|w\|_1 + v^T(z-Xw+y)\\
&= \dfrac{1}{2} \|z\|_2^2 +v^Tz + \lambda\|w\|_1 - v^TXw +v^Ty  
\end{aligned}$$
On résoud $\nabla_z\mathcal{L}=0~\Leftrightarrow ~ z=-v$

Comme pour **HW2**, résoudre
$$\begin{aligned}
\inf_w ~\lambda\|w\|_1-v^TXw &\Leftrightarrow \inf_w ~\|w\|_1-\left(\dfrac{X^Tv}{\lambda}\right)^Tw\\
&= \begin{cases}
0 ~\text{si}~\|\dfrac{X^Tv}{\lambda}\|_\infty \leq 1\\
+\infty ~\text{sinon}
\end{cases}
\end{aligned}$$

On a donc 
$$\begin{aligned}
\inf_{z,w} \mathcal{L}(z,v,w)&=\underbrace{\dfrac{1}{2} \|-v\|_2^2 +v^T(-v)}_{-\frac{1}{2}\|v\|_2^2}
+\underbrace{ \lambda\|w\|_1 - v^TXw }_{\text{s.c.} ~\lVert\dfrac{X^Tv}{\lambda}\rVert_\infty \leq 1 } +v^Ty\\
&=-\frac{1}{2}\|v\|_2^2+v^Ty \quad\quad \text{s.c.}~~\lVert\dfrac{X^Tv}{\lambda}\rVert_\infty \leq 1 
\end{aligned}$$

Le problème dual revient à maximiser cet inf, d'où 
$$
\max_v g(v)=\max_v -\frac{1}{2}\|v\|_2^2+v^Ty \Leftrightarrow \min_v \frac{1}{2}\|v\|_2^2-v^Ty
~~\text{s.c.}~~\lVert\dfrac{X^Tv}{\lambda}\rVert_\infty \leq 1 \quad\quad \text{(DUAL)}
$$

On peut dire que :
$$\begin{aligned}
\lVert\dfrac{X^Tv}{\lambda}\rVert_\infty \leq 1 &\Leftrightarrow -1 \leq \dfrac{[X^Tv]_i}{\lambda}\leq 1 
\quad\quad ~\forall i \in \llbracket 1,n \rrbracket \\ 
&\Leftrightarrow \dfrac{[X^Tv]_i}{\lambda}\leq 1 \quad\text{et}\quad-\dfrac{[X^Tv]_i}{\lambda}\leq 1 \\
&\Leftrightarrow 
\begin{pmatrix}
\frac{X^T}{\lambda}\\
-\frac{X^T}{\lambda}
\end{pmatrix}
\preccurlyeq \textbf{1}_{2d}\\ 
&\Leftrightarrow Av
\preccurlyeq \lambda \textbf{1}_{2d}
 \quad\quad \text{avec}~A=
\begin{pmatrix}
X^T\\
-X^T
\end{pmatrix}
\end{aligned}$$



En notant $\frac{1}{2}\|v\|_2^2=v^T \frac12I_nv=v^TQv$ et $y=-p,\quad b=\lambda \textbf{1}_{2d}$ on a :
$$
\min_v v^TQv +v^Tp \quad\quad \text{s.c.}
\begin{cases}
Q=\frac{1}{2}I_n\\
 Av
\preccurlyeq b
\end{cases}
$$

## Question 2
Pour la méthode des points intérieurs, on transforme notre fonction objective
$g_0(v)=v^TQv +v^Tp$ en la fonction $g_t(v)=tg_0(v)+\phi$ avec $\phi=-\sum_1^{2d}-\log  (b_i - [Av]_i)$.

On calcule le gradient et la Hessienne de $g_t$ (on note $\left(A_i\right)_{1\leq i\leq 2d} \in \mathbb{R}^n$ la $i$-ème ligne de $A$) :
$$
\nabla_v g_t(v)=t(2Qv+p)+\sum_1^{2d}\frac{A_i^T}{b_i - [Av]_i}\\
\nabla^2 g_t(v)=2tQ +\sum_1^{2d}\frac{A_iA_i^T}{\left(b_i-[Av]_i\right)^2}
$$
On a le pseudo-algorithme suivant :
>  Choisir un $v_0$ faisable, $t_0>0,~\mu>1,\epsilon>0$
> - Tant que $\frac{2d}{t}>\epsilon$ :
>> - Faire une étape de centrage, *i.e.* trouver $\min_v g_t(v)$ par  la méthode de Newton :
>>> - Tant que $\lambda^2=\nabla g_t(v) \nabla^2 g_t(v)^{-1} \nabla g_t(v)<\epsilon$ :
>>>> - Choisir un pas $\Delta v = -\nabla^2 g_t(v)^{-1} \nabla g_t(v)$
>>>> - Trouver la longueur du pas $\xi$ tel que $g_t(v+\xi \nabla v)<g_t(v)+\alpha \xi \nabla g_t(v)^T\nabla v$ (par méthode de line backtracking de paramètres $\alpha$ et $\beta$)
>>>> -  Updater $v=v+\xi \nabla v$
>> - Updater $t=\mu t$


## Question 3 

Pour évaluer l'impact de $\mu$ sur l'évalutation de $w$, on utilise la complementary slackness des conditions KKT (stricte convexité du lagrangien, stricte faisabilité):
- on pose $v^*$ solution optimale du dual (et donc du lagrangien $\mathcal{L}(z,w,v)$)
- alors on vérifie $v^{*T} \sum_1^{2d}(z-Xw+y)=0$ à $z,w$ optimaux.

On a vu plus haut que $\nabla_z \mathcal{L}(z,w,v^*)=0~\Rightarrow~z=-v^*$

Pour vérifier la complementary slackness il faut donc $-v^* -Xw+y=0 ~\Leftrightarrow ~w=X^{-1}(y-v^*)$.
