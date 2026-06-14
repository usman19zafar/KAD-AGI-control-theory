\documentclass[11pt]{article}

\usepackage[margin=1in]{geometry}
\usepackage{amsmath,amssymb,amsthm}
\usepackage{tikz}
\usetikzlibrary{arrows.meta,positioning,calc}
\usepackage{hyperref}
\usepackage{enumitem}

\theoremstyle{plain}
\newtheorem{theorem}{Theorem}
\newtheorem{proposition}{Proposition}
\newtheorem{corollary}{Corollary}
\newtheorem{lemma}{Lemma}

\theoremstyle{definition}
\newtheorem{definition}{Definition}
\newtheorem{assumption}{Assumption}

\theoremstyle{remark}
\newtheorem{remark}{Remark}

\title{A Control-Theoretic Framework for Alignment\\ Under Partial Observability: KAD Theory}

\author{
Usman Zafar, Ph.D.\\
Zulfr Inc.\\
Milton, Ontario, Canada\\
\texttt{info@zulfr.com}
}

\date{}

\begin{document}

\maketitle

\begin{abstract}
This paper presents Kinetic Alignment of Domains (KAD) Theory, a control theoretic framework for modeling misalignment and failure across engineered, cognitive, and organizational systems operating under partial observability. The domain and the agent are embedded as time varying elements of a common Hilbert space, and visibility is formalized as a time varying orthogonal projection arising from conditional expectation onto an observable sub-$\sigma$-algebra. Under an explicit \emph{representability constraint} that an agent's internal state is confined to its currently observable subspace the total misalignment between domain and agent admits an exact orthogonal decomposition into an observable alignment error and an unobservable ``dark domain'' component. This decomposition yields a rigorous \emph{Information Failure Law}: total misalignment is bounded below by the magnitude of the dark domain, with equality characterizing the best attainable alignment under partial observability. From this foundation we derive the resulting alignment dynamics, establish a Lyapunov based uniform ultimate boundedness result under explicit adaptation dominance and bounded perturbation assumptions, formulate a joint action perception (dual control) optimization problem, characterize a phase transition criterion separating stable and divergent regimes, and derive an explicit control barrier function condition for safety invariance. The framework is illustrated across engineering, artificial intelligence, cognition, and organizational governance.
\end{abstract}

\section{Introduction}

Failure across engineered, cognitive, and organizational systems exhibits striking structural regularities despite substantial differences in scale, mechanism, and domain. Traditional accounts characterize failure in terms of stochastic degradation, instability, miscalibration, or human error, yet these explanations remain fragmented and domain specific. What is missing is a unified mathematical account of \emph{why} systems fail in the presence of uncertainty, adaptation, and incomplete information.

KAD Theory proposes that such failures share a single underlying mechanism: misalignment between an agent and its operational domain under partial observability. Rather than treating failure as a breakdown of components or policies, KAD frames it as a dynamical divergence between what a system is optimizing and what its environment requires it to optimize, given the information available to it.

The central hypothesis of KAD Theory is:

\begin{quote}
\emph{Perfect alignment is impossible without full visibility of the operational domain.}
\end{quote}

This reframes alignment as a problem of control under information constraints, integrating perception, learning, adaptation, and governance into a single measurable dynamical system. Unlike the original formulation of this idea, the version developed here is built on a single explicit structural assumption that an agent's internal state can only be constructed from information it currently observes (Definition~\ref{def:representability}) from which the central results follow as theorems rather than postulates. Figure~\ref{fig:overview} summarizes how the framework unifies the four application domains discussed in Section~\ref{sec:implications}.

\begin{figure}[h]
\centering
\resizebox{0.92\textwidth}{!}{
\begin{tikzpicture}[
  >=Stealth,
  box/.style={draw, rounded corners, align=center, minimum width=3.4cm, minimum height=1.1cm, font=\small},
  core/.style={draw, rounded corners, align=center, minimum width=4.0cm, minimum height=1.3cm, font=\bfseries\small, fill=gray!12}
]
\node[core] (core) at (0,0) {KAD Theory:\\Agent--Domain Alignment\\under Partial Observability};
\node[box] (eng) at (-5.0,2.4) {Engineered\\Systems};
\node[box] (ai)  at (5.0,2.4)  {Artificial\\Intelligence};
\node[box] (cog) at (-5.0,-2.4) {Human\\Cognition};
\node[box] (org) at (5.0,-2.4)  {Organizations};
\node[box, fill=gray!5] (out) at (0,-3.8) {Alignment Dynamics, Stability,\\and Safety Under Partial Observability};

\draw[->] (eng) -- (core);
\draw[->] (ai) -- (core);
\draw[->] (cog) -- (core);
\draw[->] (org) -- (core);
\draw[->] (core) -- (out);
\end{tikzpicture}
}
\caption{KAD Theory as a unifying framework: four application domains map onto a single Hilbert-space model of agent--domain interaction under partial observability.}
\label{fig:overview}
\end{figure}

\section{Mathematical Foundations}
\label{sec:foundations}

\subsection{State Space}

Let $(X,\mathcal{F},\mu)$ be a $\sigma$-finite measure space, where $X$ represents the space of semantic features relevant to a given domain (task descriptors, environment variables, sensory channels, organizational signals), $\mathcal{F}$ is a $\sigma$-algebra of measurable subsets of $X$, and $\mu$ is a reference measure. Define the real Hilbert space
\[
H := L^2(X,\mathcal{F},\mu), \qquad \langle f,g\rangle = \int_X f g \, d\mu, \qquad \|f\| = \langle f,f\rangle^{1/2}.
\]
The domain state $D(t)\in H$ and the agent state $A(t)\in H$ are absolutely continuous curves $D,A:[0,\infty)\to H$, representing respectively the true environment/task structure and the agent's internal model, policy, or strategy. Embedding both processes in $L^2$ provides a metric and geometric structure suitable for orthogonal projections, conditional expectations, and Lyapunov analysis, enabling a unified treatment of perception, adaptation, and misalignment.

\subsection{Visibility Operator}

For each $t\ge 0$, let $\mathcal{F}_{\mathrm{obs}}(t)\subseteq\mathcal{F}$ be a sub-$\sigma$-algebra representing all information available to the agent at time $t$ (sensor readings, measurements, attention allocation, governance disclosures). Define the closed subspace
\[
H_{\mathrm{obs}}(t) := L^2(X,\mathcal{F}_{\mathrm{obs}}(t),\mu) \subseteq H.
\]
The \emph{visibility operator} is the conditional expectation
\[
P(t) := \mathbb{E}[\,\cdot \mid \mathcal{F}_{\mathrm{obs}}(t)\,] : H \to H.
\]
It is a standard fact in $L^2$ theory \cite{billingsley1995} that conditional expectation coincides with orthogonal projection onto $H_{\mathrm{obs}}(t)$. Consequently
\[
P(t)^2 = P(t), \qquad P(t)^* = P(t), \qquad \|P(t)\|_{\mathrm{op}} \le 1,
\]
and $H$ decomposes as the orthogonal direct sum
\[
H = H_{\mathrm{obs}}(t) \oplus H_{\mathrm{dark}}(t), \qquad H_{\mathrm{dark}}(t) := H_{\mathrm{obs}}(t)^\perp = \mathrm{Null}(P(t)) = \mathrm{Range}(I-P(t)).
\]
For any $f\in H$, the \emph{dark component} of $f$ at time $t$ is $(I-P(t))f \in H_{\mathrm{dark}}(t)$: the portion of $f$ that cannot be inferred or acted upon from currently observable information. We write $D_{\mathrm{dark}}(t) := (I-P(t))D(t)$.

\subsection{Representability Constraint}

The original formulation of KAD Theory defined alignment error as $E(t) = \|P(t)D(t)-P(t)A(t)\|$ and asserted that this quantity is bounded below by $\|(I-P(t))D(t)\|$. This claim does not follow from the definitions as stated: $P(t)D(t)-P(t)A(t)\in H_{\mathrm{obs}}(t)$ and $(I-P(t))D(t)\in H_{\mathrm{dark}}(t)$ occupy \emph{orthogonal complementary subspaces}, and an agent free to choose any $A(t)\in H$ could set $P(t)A(t)=P(t)D(t)$ exactly, driving the claimed error to zero independent of the dark domain's size. A genuine information-theoretic floor on alignment requires an additional structural constraint linking the agent's representational freedom to what it can observe. KAD Theory adopts the following constraint, which we take to be definitional of an \emph{information-bounded agent}.

\begin{definition}[Representability Constraint]
\label{def:representability}
An agent state $A(t)\in H$ is \emph{representable} at time $t$ if $A(t)\in H_{\mathrm{obs}}(t)$, equivalently $A(t)=P(t)A(t)$. KAD Theory assumes that $A(t)$ is representable for all $t\ge 0$: an agent's internal model can be constructed only from information to which it currently has access.
\end{definition}

Definition~\ref{def:representability} is the single structural assumption from which the Information--Failure Law (Theorem~\ref{thm:ifl}) follows as a theorem rather than a postulate.

\subsection{Alignment Error and Total Misalignment}

Given Definition~\ref{def:representability}, define:
\begin{itemize}[itemsep=2pt]
\item \textbf{Total misalignment:} $\Delta(t) := D(t) - A(t) \in H$.
\item \textbf{Observable alignment error:} $E(t) := P(t)\Delta(t) = P(t)D(t) - A(t)$, using $P(t)A(t)=A(t)$.
\item \textbf{Dark-domain component:} since $(I-P(t))A(t)=0$, we have $(I-P(t))\Delta(t) = (I-P(t))D(t) = D_{\mathrm{dark}}(t)$.
\end{itemize}
Thus
\[
\Delta(t) = E(t) + D_{\mathrm{dark}}(t), \qquad E(t)\in H_{\mathrm{obs}}(t), \quad D_{\mathrm{dark}}(t)\in H_{\mathrm{dark}}(t),
\]
an orthogonal decomposition of the total misalignment into an observable, correctable component $E(t)$ and an unobservable, structurally irreducible component $D_{\mathrm{dark}}(t)$. This decomposition, illustrated in Figure~\ref{fig:foundations}, is the geometric core of KAD Theory.

\begin{figure}[h]
\centering
\begin{tikzpicture}[>=Stealth, scale=1]
\draw[->] (-0.6,0) -- (5.2,0) node[right] {$H_{\mathrm{obs}}(t)$ (observable)};
\draw[->] (0,-0.6) -- (0,3.6) node[above] {$H_{\mathrm{dark}}(t)$ (dark)};

\draw[thick,->,blue] (0,0) -- (3.6,2.6) node[above right] {$D(t)$};
\draw[dashed] (3.6,2.6) -- (3.6,0);
\draw[dashed] (3.6,2.6) -- (0,2.6);
\draw[thick,->,red] (0,0) -- (3.6,0) node[below] {$P(t)D(t)$};
\draw[thick,->,red] (0,0) -- (0,2.6) node[left] {$D_{\mathrm{dark}}(t)=(I-P(t))D(t)$};

\draw[thick,->,green!60!black] (0,0) -- (1.9,0) node[below] {$A(t)$};

\draw[thick,<->,purple] (1.9,-0.8) -- (3.6,-0.8);
\node[purple,below] at (2.75,-0.95) {$E(t)=P(t)D(t)-A(t)$};
\draw[dotted] (1.9,0) -- (1.9,-0.8);
\draw[dotted] (3.6,0) -- (3.6,-0.8);
\end{tikzpicture}
\caption{Orthogonal decomposition of the Hilbert space $H = H_{\mathrm{obs}}(t)\oplus H_{\mathrm{dark}}(t)$. The agent state $A(t)$ is confined to $H_{\mathrm{obs}}(t)$ by Definition~\ref{def:representability}; the observable error $E(t)$ lies in $H_{\mathrm{obs}}(t)$ while the dark-domain component $D_{\mathrm{dark}}(t)$ lies in the orthogonal complement.}
\label{fig:foundations}
\end{figure}

\section{System Dynamics}
\label{sec:dynamics}

We now derive the time evolution of the observable alignment error $E(t)$.

\begin{itemize}[itemsep=2pt]
\item \textbf{Domain drift:} $W(t) := \dot{D}(t) \in H$, the exogenous rate of change of the environment or task structure.
\item \textbf{Agent update law:} the agent's internal state evolves according to
\[
\dot{A}(t) = U(t) - \lambda A(t),
\]
where $U(t)\in H$ is the agent's \emph{corrective adaptation policy} (e.g.\ a learning or control update derived from observed signals) and $\lambda \ge 0$ is a constant \emph{representational decay rate} modeling forgetting, focus loss, or degradation of internal structure in the absence of reinforcement. Both contributions are standard modeling primitives in adaptive control and learning theory \cite{bertsekas2017,khalil2002}.
\end{itemize}

Differentiating $E(t) = P(t)D(t) - A(t)$ and applying the product rule for operator-valued functions,
\[
\dot{E}(t) = \dot{P}(t)D(t) + P(t)\dot{D}(t) - \dot{A}(t)
= \dot{P}(t)D(t) + P(t)W(t) - U(t) + \lambda A(t).
\]
Collecting terms gives the \textbf{alignment dynamics}:
\begin{equation}
\dot{E}(t) = P(t)W(t) \;-\; U(t) \;+\; \lambda A(t) \;+\; \dot{P}(t)D(t).
\label{eq:dynamics}
\end{equation}
Each term has a distinct interpretation:
\begin{itemize}[itemsep=2pt]
\item $P(t)W(t)$ -- the \emph{visible drift}: the projection of environmental change onto currently observable coordinates, the primary driver of misalignment growth.
\item $-U(t)$ -- \emph{corrective adaptation}: the agent's deliberate response to observed error.
\item $\lambda A(t)$ -- \emph{representational decay}: leakage of the agent's internal state, increasing $E(t)$ in the absence of reinforcement.
\item $\dot{P}(t)D(t)$ -- the \emph{visibility-shift term}: changes in the observable subspace itself, which can reveal previously dark structure (increasing $\|E(t)\|$ transiently) or conceal previously visible structure.
\end{itemize}
For convenience we define the \emph{perturbation term} $\xi(t) := \lambda A(t) + \dot{P}(t)D(t)$, collecting the two contributions that are not directly shaped by the agent's corrective policy. Equation~\eqref{eq:dynamics} then reads $\dot{E}(t) = P(t)W(t) - U(t) + \xi(t)$, the form used throughout the stability and safety analyses of Sections~\ref{sec:stability} and~\ref{sec:safety}. Figure~\ref{fig:dynamics} depicts this signal-flow structure.

\begin{figure}[h]
\centering
\resizebox{0.92\textwidth}{!}{
\begin{tikzpicture}[>=Stealth, font=\small,
  block/.style={draw, rectangle, minimum height=1cm, minimum width=3.0cm, align=center},
  sum/.style={draw, circle, minimum size=0.7cm, inner sep=0pt}]

\node[sum] (sum) {$\Sigma$};
\node[block, right=1.8cm of sum] (int) {Integrator\\$E(t)=\int_0^t \dot E(s)\,ds$};
\node[block, below=1.6cm of int] (adapt) {Adaptation Policy\\$U(t)$};
\node[block, above left=0.7cm and 1.0cm of sum] (drift) {Visible Drift\\$P(t)W(t)$};
\node[block, below left=0.7cm and 1.0cm of sum] (pert) {Perturbation\\$\xi(t)=\lambda A(t)+\dot P(t)D(t)$};

\draw[->] (drift) -- (sum);
\draw[->] (pert) -- (sum);
\draw[->] (sum) -- (int);
\draw[->] (int.east) -- ++(1.3,0) node[right] {$E(t)$};
\draw[->] ($(int.east)+(1.3,0)$) |- (adapt.east);
\draw[->] (adapt.west) -| node[pos=0.95,left] {$-U(t)$} (sum.south);
\end{tikzpicture}
}
\caption{Block-diagram representation of the alignment dynamics \eqref{eq:dynamics}. Visible drift and perturbation terms enter a summing junction whose integral is the alignment error $E(t)$; the adaptation policy $U(t)$ closes the loop as negative feedback.}
\label{fig:dynamics}
\end{figure}

\section{The Information--Failure Law}
\label{sec:ifl}

\begin{proposition}[Orthogonal Decomposition of Misalignment]
\label{prop:pythagoras}
For all $t\ge 0$,
\[
\|\Delta(t)\|^2 = \|E(t)\|^2 + \|D_{\mathrm{dark}}(t)\|^2.
\]
\end{proposition}

\begin{proof}
By Section~\ref{sec:foundations}, $\Delta(t) = E(t) + D_{\mathrm{dark}}(t)$ with $E(t)\in H_{\mathrm{obs}}(t)$ and $D_{\mathrm{dark}}(t)\in H_{\mathrm{dark}}(t)=H_{\mathrm{obs}}(t)^\perp$. Since $H_{\mathrm{obs}}(t)\perp H_{\mathrm{dark}}(t)$, $\langle E(t), D_{\mathrm{dark}}(t)\rangle = 0$, and
\[
\|\Delta(t)\|^2 = \|E(t)+D_{\mathrm{dark}}(t)\|^2 = \|E(t)\|^2 + 2\langle E(t),D_{\mathrm{dark}}(t)\rangle + \|D_{\mathrm{dark}}(t)\|^2 = \|E(t)\|^2+\|D_{\mathrm{dark}}(t)\|^2. \qedhere
\]
\end{proof}

\begin{theorem}[Information--Failure Law]
\label{thm:ifl}
For all $t\ge 0$,
\[
\|\Delta(t)\| \;\ge\; \|D_{\mathrm{dark}}(t)\| = \|(I-P(t))D(t)\|,
\]
with equality if and only if $E(t)=0$.
\end{theorem}

\begin{proof}
By Proposition~\ref{prop:pythagoras}, $\|\Delta(t)\|^2 = \|E(t)\|^2+\|D_{\mathrm{dark}}(t)\|^2 \ge \|D_{\mathrm{dark}}(t)\|^2$ since $\|E(t)\|^2\ge 0$, with equality iff $\|E(t)\|^2=0$, i.e.\ $E(t)=0$.
\end{proof}

\begin{corollary}[Full Observability is Necessary for Perfect Alignment]
\label{cor:full-obs}
If $\Delta(t)=0$ (perfect alignment) for some $t$, then $D_{\mathrm{dark}}(t)=0$, i.e.\ $H_{\mathrm{obs}}(t)=H$ (full observability at time $t$). Conversely, if $H_{\mathrm{obs}}(t)\subsetneq H$, the best attainable state is $E(t)=0$, in which case $\Delta(t)=D_{\mathrm{dark}}(t)\ne 0$.
\end{corollary}

\begin{remark}
Theorem~\ref{thm:ifl} corrects and sharpens the informal claim in earlier formulations of KAD Theory. It is the \emph{total} misalignment $\Delta(t)$ -- not the observable error $E(t)$ -- that is bounded below by the magnitude of the dark domain. The observable error $E(t)$ can in principle be driven to zero by sufficiently effective adaptation (Section~\ref{sec:stability}); the irreducible residual of misalignment is precisely the unobservable component of the domain, geometrically orthogonal to everything the agent can perceive or correct.
\end{remark}

\begin{figure}[h]
\centering
\begin{tikzpicture}[>=Stealth]
\coordinate (O) at (0,0);
\coordinate (Ex) at (4.2,0);
\coordinate (Dy) at (4.2,2.6);
\draw[thick] (O) -- (Ex) -- (Dy) -- cycle;
\draw[thick,->,red] (O) -- (Ex);
\node[below] at (2.1,-0.05) {$E(t)$};
\draw[thick,->,blue] (Ex) -- (Dy);
\node[right] at (4.3,1.3) {$D_{\mathrm{dark}}(t)$};
\draw[thick,->,purple] (O) -- (Dy);
\node[above left] at (1.8,1.5) {$\Delta(t)=D(t)-A(t)$};
\draw (3.9,0) -- (3.9,0.3) -- (4.2,0.3);
\end{tikzpicture}
\caption{Pythagorean structure of Theorem~\ref{thm:ifl}: total misalignment $\Delta(t)$ is the hypotenuse of a right triangle whose legs are the observable error $E(t)$ and the dark-domain component $D_{\mathrm{dark}}(t)$. Hence $\|\Delta(t)\|\ge\|D_{\mathrm{dark}}(t)\|$ always, with equality when $E(t)=0$.}
\label{fig:ifl}
\end{figure}

\section{Stability Analysis}
\label{sec:stability}

Stability in KAD Theory concerns whether the observable alignment error $E(t)$ remains bounded despite drift, decay, and visibility shifts. We use the quadratic Lyapunov candidate
\[
V(E) := \|E(t)\|^2,
\]
whose derivative along trajectories of \eqref{eq:dynamics} is
\[
\dot{V}(t) = 2\langle E(t),\dot{E}(t)\rangle = 2\langle E(t), P(t)W(t)-U(t)+\xi(t)\rangle,
\qquad \xi(t)=\lambda A(t)+\dot P(t)D(t).
\]

\begin{assumption}[Adaptation Dominance]
\label{ass:dominance}
There exists $c>0$ such that for all $t\ge 0$,
\[
\langle E(t),\, U(t)-P(t)W(t)\rangle \;\ge\; c\,\|E(t)\|^2.
\]
\end{assumption}

\begin{assumption}[Bounded Perturbation]
\label{ass:bounded}
There exists $\delta\ge 0$ such that $\|\xi(t)\|\le\delta$ for all $t\ge 0$.
\end{assumption}

Assumption~\ref{ass:dominance} formalizes the requirement that the agent's corrective adaptation, net of visible drift, points in a direction that reduces misalignment at least proportionally to the current error -- a sector (contraction) condition standard in adaptive control \cite{khalil2002}. Assumption~\ref{ass:bounded} requires that representational decay and visibility-shift effects remain bounded.

\begin{proposition}[Uniform Ultimate Boundedness]
\label{prop:uub}
Under Assumptions~\ref{ass:dominance} and~\ref{ass:bounded},
\[
\dot{V}(t) \;\le\; -c\,\|E(t)\|^2 + \frac{\delta^2}{c} \qquad \text{for all } t\ge 0,
\]
and consequently
\[
\limsup_{t\to\infty}\|E(t)\| \;\le\; \frac{\delta}{c}.
\]
\end{proposition}

\begin{proof}
Write $\dot{V}(t)=2\langle E,P(t)W-U\rangle+2\langle E,\xi\rangle = -2\langle E,U-P(t)W\rangle+2\langle E,\xi\rangle$. By Assumption~\ref{ass:dominance}, $-2\langle E,U-P(t)W\rangle \le -2c\|E\|^2$. By the Cauchy--Schwarz inequality and Assumption~\ref{ass:bounded}, $2\langle E,\xi\rangle \le 2\|E\|\,\|\xi\| \le 2\delta\|E\|$. Hence
\[
\dot V(t) \le -2c\|E(t)\|^2 + 2\delta\|E(t)\|.
\]
By Young's inequality, for any $a,b\ge 0$ and $c>0$, $2ab \le c a^2 + b^2/c$; with $a=\|E(t)\|$ and $b=\delta$,
\[
2\delta\|E(t)\| \le c\|E(t)\|^2 + \frac{\delta^2}{c}.
\]
Substituting,
\[
\dot V(t) \le -2c\|E(t)\|^2 + c\|E(t)\|^2 + \frac{\delta^2}{c} = -c\|E(t)\|^2+\frac{\delta^2}{c}.
\]
Whenever $\|E(t)\|>\delta/c$, $\dot V(t)<0$, so $V(t)$ strictly decreases until $\|E(t)\|\le\delta/c$. By the standard comparison lemma for Lyapunov functions \cite{khalil2002}, $\limsup_{t\to\infty}\|E(t)\|\le \delta/c$.
\end{proof}

\begin{corollary}[Bound on Total Misalignment]
\label{cor:total-bound}
Under Assumptions~\ref{ass:dominance} and~\ref{ass:bounded},
\[
\limsup_{t\to\infty}\|\Delta(t)\|^2 \;\le\; \left(\frac{\delta}{c}\right)^2 + \sup_{t\ge 0}\|D_{\mathrm{dark}}(t)\|^2.
\]
\end{corollary}

\begin{proof}
Immediate from Proposition~\ref{prop:pythagoras} and Proposition~\ref{prop:uub}.
\end{proof}

\noindent Proposition~\ref{prop:uub} states that the system is stable but never perfectly aligned: adaptation drives $E(t)$ into a ball of radius $\delta/c$, while Theorem~\ref{thm:ifl} guarantees that total misalignment never falls below the dark-domain magnitude. Together these results formalize the central claim of KAD Theory -- alignment is stable but bounded away from zero whenever either $\delta>0$ or $D_{\mathrm{dark}}(t)\ne 0$.

\begin{figure}[h]
\centering
\begin{tikzpicture}[>=Stealth]
\draw[gray!45] (0,0) circle (3);
\draw[gray!45] (0,0) circle (2);
\draw[gray!45] (0,0) circle (1);
\draw[thick,dashed,red] (0,0) circle (0.6);
\node[red] at (1.55,-0.55) {$\|E\|\le \delta/c$};
\draw[thick,blue,->] plot[domain=0:4*pi, samples=150] ({(3-2.4*\x/(4*pi))*cos(\x*180/pi)}, {(3-2.4*\x/(4*pi))*sin(\x*180/pi)});
\fill (0,0) circle (0.03);
\node at (0,3.3) {level sets of $V(E)=\|E\|^2$};
\end{tikzpicture}
\caption{Lyapunov-based stability picture. Trajectories of $E(t)$ spiral inward across level sets of $V(E)=\|E\|^2$ and converge to (but do not collapse into) the ball $\{E:\|E\|\le\delta/c\}$, per Proposition~\ref{prop:uub}.}
\label{fig:stability}
\end{figure}

\section{Dual Control: Joint Optimization of Action and Perception}
\label{sec:dual}

Classical control theory assumes that the controller has fixed access to observations. KAD Theory breaks this assumption by treating perception itself as a controllable resource: the agent jointly selects an adaptation policy $U(t)$ and a visibility operator $P(t)$ by solving
\begin{equation}
(U^*,P^*) = \arg\min_{U,\,P} \; \mathbb{E}\Big[\,\|E(t)\|^2 + \gamma\,C(P(t))\,\Big]
\quad \text{subject to } \eqref{eq:dynamics} \text{ and Definition~\ref{def:representability}},
\label{eq:dualcontrol}
\end{equation}
where $C(P(t))$ is the cost of acquiring or maintaining a given level of visibility (sensing, computation, interpretability, governance overhead), and $\gamma>0$ trades off alignment accuracy against information cost.

\subsection{Why This Is a Genuinely Joint Problem}

In standard dual control \cite{feldbaum1960}, an action policy and an information-gathering policy are optimized jointly, but the admissible set for the action policy does not itself depend on the information policy. In \eqref{eq:dualcontrol}, by contrast, Definition~\ref{def:representability} requires $A(t)\in H_{\mathrm{obs}}(t)$ for \emph{every} choice of $P(t)$: changing $P(t)$ changes both (i) the projection through which error is measured, $E(t)=P(t)D(t)-A(t)$, and (ii) the admissible set $H_{\mathrm{obs}}(t)$ in which $A(t)$ itself must lie. This second effect has no analogue in classical dual control and is the source of KAD Theory's distinctive coupling between perception and representation.

\subsection{First-Order Structure}

Treating $U$ and $P$ as the controls and $E$ as the resulting state via \eqref{eq:dynamics}, the Fr\'echet (functional) derivatives of the objective in \eqref{eq:dualcontrol} with respect to $U$ and $P$ vanish at an optimum:
\[
\frac{\delta}{\delta U}\,\mathbb{E}\big[\|E(t)\|^2\big] = 0,
\qquad
\frac{\delta}{\delta P}\,\mathbb{E}\big[\|E(t)\|^2 + \gamma C(P(t))\big] = 0,
\]
subject to the coupling constraint $A(t)=P(t)A(t)$. The first condition yields an \emph{action policy} $U^*(t)$ that minimizes projected misalignment given the current visibility structure -- the classical adaptive-control component. The second yields a \emph{sensing policy} $P^*(t)$ that balances the marginal reduction in $\|E(t)\|^2$ (and, via Theorem~\ref{thm:ifl}, in $\|D_{\mathrm{dark}}(t)\|$) against the marginal increase in visibility cost $\gamma C(P(t))$.

\subsection{Significance}

The sensing policy $P^*(t)$ may expand the observable subspace, reallocate attention, increase interpretability, or request additional governance-level visibility -- each of which shrinks $H_{\mathrm{dark}}(t)$ and therefore, by Theorem~\ref{thm:ifl}, lowers the irreducible floor on total misalignment. KAD Theory thus treats perception as a strategic decision with a direct, quantified effect on the best attainable alignment, providing a mathematical foundation for adaptive interpretability, dynamic oversight, attention allocation, and governance-aware sensing.

\begin{figure}[h]
\centering
\resizebox{0.85\textwidth}{!}{
\begin{tikzpicture}[>=Stealth, font=\small,
  block/.style={draw, rectangle, rounded corners, minimum height=1.1cm, minimum width=3.6cm, align=center}]
\node[block] (obj) at (0,0) {Joint Objective\\$\mathbb{E}[\|E(t)\|^2+\gamma C(P(t))]$};
\node[block, above left=1.8cm and 1.6cm of obj] (act) {Action Control\\optimize $U(t)$\\(reduce $\|E(t)\|$)};
\node[block, above right=1.8cm and 1.6cm of obj] (perc) {Perception Control\\optimize $P(t)$\\(reduce $\|D_{\mathrm{dark}}(t)\|$, cost $C(P)$)};
\node[block, below=1.8cm of obj] (state) {Coupled State\\$(E(t),\,P(t))$ via Definition~\ref{def:representability}};

\draw[->] (act) -- (obj);
\draw[->] (perc) -- (obj);
\draw[->] (obj) -- (state);
\draw[->] (state) -| (act.south);
\draw[->] (state) -| (perc.south);
\end{tikzpicture}
}
\caption{Dual control in KAD Theory: action and perception policies are optimized jointly against a single objective, and both are coupled to the same evolving state through the representability constraint.}
\label{fig:dual}
\end{figure}

\section{Phase Transition Criterion}
\label{sec:phase}

The alignment dynamics exhibit a phase transition governed by the relative strength of adaptation versus visible drift. Define the \emph{stability ratio}
\[
\rho(t) := \frac{\|U(t)\|}{\|P(t)W(t)\|}, \qquad \text{whenever } \|P(t)W(t)\|>0.
\]

\begin{remark}[Relation to Assumption~\ref{ass:dominance}]
\label{rem:rho}
The ratio $\rho(t)$ is a cheaply computable, real-time diagnostic, but it is not equivalent to Assumption~\ref{ass:dominance}. By Cauchy--Schwarz, $\langle E,U-P(t)W\rangle \ge \|E\|\big(\|U\|\cos\theta_U-\|P(t)W\|\cos\theta_W\big)$ for the angles $\theta_U,\theta_W$ between $E(t)$ and $U(t),P(t)W(t)$ respectively. Thus $\rho(t)>1$ is \emph{necessary but not sufficient} for Assumption~\ref{ass:dominance} to hold with $c>0$: sufficiency additionally requires that $U(t)$ be suitably aligned with $-E(t)$ (i.e.\ $\cos\theta_U$ not too small) relative to the alignment of $P(t)W(t)$ with $E(t)$. $\rho(t)$ should therefore be read as an early-warning indicator that complements, rather than replaces, Assumption~\ref{ass:dominance}.
\end{remark}

\paragraph{$\rho>1$: Adaptation-Dominant Regime (Stable Phase).} When the corrective policy's magnitude exceeds the visible drift's magnitude and the directional condition of Remark~\ref{rem:rho} holds, Assumption~\ref{ass:dominance} is satisfied with some $c>0$, and Proposition~\ref{prop:uub} guarantees uniform ultimate boundedness of $E(t)$.

\paragraph{$\rho<1$: Drift-Dominant Regime (Divergent Phase).} When visible drift overwhelms the agent's corrective capacity, no choice of directions can restore Assumption~\ref{ass:dominance}: $\|U(t)-P(t)W(t)\|$ cannot be made to shrink $\|E(t)\|$ on average, and misalignment grows without the boundedness guarantee of Section~\ref{sec:stability}.

\paragraph{Critical Threshold.} The boundary $\rho=1$ marks the transition between these regimes. Small changes in visibility (which alter $P(t)W(t)$), adaptation capacity (which alters $U(t)$), or drift can push the system across this boundary, making $\rho(t)$ a central diagnostic for governance and control.

\begin{figure}[h]
\centering
\begin{tikzpicture}[>=Stealth]
\fill[red!12] (-3,0) rectangle (0,2.4);
\fill[green!12] (0,0) rectangle (3,2.4);
\draw[->] (-3.3,0) -- (3.6,0) node[right] {$\rho$};
\draw[dashed,thick] (0,0) -- (0,2.6);
\node at (0,2.85) {$\rho=1$ (critical threshold)};
\node[align=center] at (-1.5,1.2) {Drift-Dominant\\Divergent Phase};
\node[align=center] at (1.5,1.2) {Adaptation-Dominant\\Stable Phase};
\end{tikzpicture}
\caption{Phase transition in $\rho=\|U(t)\|/\|P(t)W(t)\|$. The boundary $\rho=1$ separates a drift-dominant divergent phase from an adaptation-dominant stable phase, subject to the directional conditions of Remark~\ref{rem:rho}.}
\label{fig:phase}
\end{figure}

\section{Safety and Invariance}
\label{sec:safety}

Safety in KAD Theory requires that the alignment error $E(t)$ remain within a prescribed admissible region despite drift, decay, and partial observability. Define the \emph{safe set}
\[
S := \{E\in H_{\mathrm{obs}} : \|E\|\le\epsilon\}, \qquad \epsilon>0,
\]
and the \emph{barrier function}
\[
h(E) := \epsilon^2-\|E\|^2 = \epsilon^2 - V(E).
\]
Note $h(E)\ge 0 \iff E\in S$, and $\dot h(t) = -\dot V(t)$. Forward invariance of $S$ is guaranteed by the standard control-barrier-function (CBF) condition \cite{ames2019}
\[
\dot h(t) \;\ge\; -\kappa h(t), \qquad \kappa>0,
\]
which, written in terms of $V$, reads
\[
\dot V(t) \;\le\; \kappa\big(\epsilon^2-\|E(t)\|^2\big).
\]

\begin{proposition}[Safety Invariance]
\label{prop:safety}
Suppose Assumptions~\ref{ass:dominance} and~\ref{ass:bounded} hold with constants $c,\delta$, and suppose $0<\kappa\le c$ and $\delta\le\epsilon\sqrt{c\kappa}$. Then the safe set $S=\{E:\|E\|\le\epsilon\}$ is forward invariant under \eqref{eq:dynamics}: if $\|E(0)\|\le\epsilon$, then $\|E(t)\|\le\epsilon$ for all $t\ge0$.
\end{proposition}

\begin{proof}
By Proposition~\ref{prop:uub}, $\dot V(t)\le -c\|E(t)\|^2+\delta^2/c$ for all $t$. It suffices to show
\[
-c\|E\|^2+\frac{\delta^2}{c} \;\le\; \kappa\epsilon^2-\kappa\|E\|^2 \qquad \text{for all } \|E\|\in[0,\epsilon],
\]
i.e.\ $(\kappa-c)\|E\|^2 \le \kappa\epsilon^2-\delta^2/c$. Since $\kappa\le c$, the left-hand side is non-positive and is maximized (closest to zero) at $\|E\|=0$, where it equals $0$. Hence it suffices that
\[
0 \;\le\; \kappa\epsilon^2-\frac{\delta^2}{c}
\quad\iff\quad
\delta^2 \le c\kappa\epsilon^2
\quad\iff\quad
\delta\le\epsilon\sqrt{c\kappa},
\]
which holds by hypothesis. Therefore $\dot V(t)\le\kappa(\epsilon^2-\|E(t)\|^2)$, i.e.\ $\dot h(t)\ge-\kappa h(t)$, for all $E(t)\in S$. By the standard comparison lemma for control barrier functions \cite{ames2019}, $S$ is forward invariant.
\end{proof}

\noindent Proposition~\ref{prop:safety} makes safety a structural property of the KAD system: governance and oversight mechanisms can be interpreted as actions that adjust $P$, $U$, $\kappa$, $\epsilon$, $c$, or $\delta$ so that the inequalities $\kappa\le c$ and $\delta\le\epsilon\sqrt{c\kappa}$ are maintained.

\begin{figure}[h]
\centering
\begin{tikzpicture}[>=Stealth]
\draw[gray!40] (0,0) circle (1);
\draw[gray!40] (0,0) circle (1.5);
\draw[thick] (0,0) circle (2);
\node at (0,2.35) {$S=\{E:\|E\|\le\epsilon\}$};
\fill (0,0) circle (0.03);
\draw[thick,blue,->] plot[domain=0:3, samples=100] ({1.8*exp(-0.3*\x)*cos(2*\x*180/pi)}, {1.8*exp(-0.3*\x)*sin(2*\x*180/pi)});
\node[blue] at (1.55,1.25) {trajectory $E(t)$};
\end{tikzpicture}
\caption{Safety invariance (Proposition~\ref{prop:safety}). Under the stated conditions on $c,\delta,\kappa,\epsilon$, trajectories of $E(t)$ that begin inside the safe set $S$ remain inside $S$ for all $t\ge0$.}
\label{fig:safety}
\end{figure}

\section{Implications}
\label{sec:implications}

Because misalignment under partial observability is a structural phenomenon governed by Theorem~\ref{thm:ifl}, Propositions~\ref{prop:uub} and~\ref{prop:safety}, and the dual control formulation of Section~\ref{sec:dual}, KAD Theory yields concrete implications across engineered systems, artificial intelligence, human cognition, and organizations, summarized in Figure~\ref{fig:implications}.

\subsection{Engineering Systems}

In engineered control systems, failure often arises from unmodeled dynamics, sensor limitations, and latent disturbances. KAD Theory formalizes these as the dark domain component $D_{\mathrm{dark}}(t)=(I-P(t))D(t)$ that cannot be sensed, insufficient corrective authority when Assumption~\ref{ass:dominance} fails ($\rho<1$, Section~\ref{sec:phase}), and loss of invariance (Proposition~\ref{prop:safety}) when drift or decay overwhelms adaptation. This reframes robustness and fault tolerance as information-bounded alignment problems with quantifiable margins ($c$, $\delta$, $\epsilon$, $\kappa$).

\subsection{Artificial Intelligence}

In AI systems, $W(t)$ corresponds to distribution shift, $D_{\mathrm{dark}}(t)$ to hidden or emergent capabilities not captured by current evaluations, $P(t)$ to interpretability and oversight coverage, and $\lambda A(t)$ to policy drift or catastrophic forgetting. Theorem~\ref{thm:ifl} shows that total misalignment is irreducible below $\|D_{\mathrm{dark}}(t)\|$ unless visibility is expanded -- formalizing concerns raised in the AI safety literature about the limits of evaluation and oversight \cite{amodei2016,russell2019}, and providing a mathematical target for interpretability and monitoring research (Section~\ref{sec:dual}).

\subsection{Human Cognition}

Human cognitive failure attention lapses, situational awareness loss, and misjudgment emerges naturally from the KAD framework. Attention limits correspond to a restricted visibility operator $P(t)$, representational decay models forgetting and focus loss via $\lambda$, and bounded rationality arises from the cost term $C(P(t))$ in the dual control objective \eqref{eq:dualcontrol}. KAD thus treats perception and attention as strategic, resource bounded processes rather than passive constraints.

\subsection{Organizations}

Organizational failure is reinterpreted as strategic misalignment under incomplete information. Different units operate with distinct visibility operators $P_i(t)$ and hence distinct observable errors $E_i(t)$ and dark components $D_{\mathrm{dark},i}(t)$; governance defines an admissible region analogous to the safe set $S$ of Section~\ref{sec:safety}; and coordination failures arise from misaligned projections of the same underlying domain $D(t)$ across units. KAD provides a mathematical foundation for organizational design and governance by showing precisely how information asymmetry and drift drive systemic misalignment, and how expanding cross unit visibility lowers the irreducible floor on total misalignment.

\begin{figure}[h]
\centering
\resizebox{0.95\textwidth}{!}{
\begin{tikzpicture}[>=Stealth, font=\small,
  domain/.style={draw, rounded corners, align=left, text width=5.0cm, minimum height=1.5cm},
  core/.style={draw, rounded corners, align=center, fill=gray!12, minimum width=3.6cm, minimum height=1.3cm, font=\bfseries\small}]
\node[core] (core) at (0,0) {KAD Core Operators\\$(P,D,A,W,U,\lambda)$};

\node[domain] (eng) at (-5.6,2.8) {\textbf{Engineering}: $D_{\mathrm{dark}}=(I-P)D$ = unmodeled dynamics; $\rho<1$ = insufficient corrective authority};
\node[domain] (ai) at (5.6,2.8) {\textbf{AI}: $W$ = distribution shift; $D_{\mathrm{dark}}$ = hidden capabilities; $P$ = oversight coverage};
\node[domain] (cog) at (-5.6,-2.8) {\textbf{Cognition}: $P$ = attention; $\lambda$ = forgetting; $C(P)$ = bounded rationality};
\node[domain] (org) at (5.6,-2.8) {\textbf{Organizations}: $P_i$ = unit visibility; coordination failure = misaligned $E_i$};

\draw[->] (core) -- (eng);
\draw[->] (core) -- (ai);
\draw[->] (core) -- (cog);
\draw[->] (core) -- (org);
\end{tikzpicture}
}
\caption{Mapping of KAD Theory's core operators onto four application domains.}
\label{fig:implications}
\end{figure}

\section{Conclusion}

KAD Theory provides a unified mathematical foundation for understanding alignment, failure, and stability across engineered, cognitive, artificial, and organizational systems. By embedding agent domain interaction within a Hilbert space geometry, introducing visibility as an operator level constraint (Section~\ref{sec:foundations}), and imposing a single representability constraint (Definition~\ref{def:representability}), the framework reframes alignment as an information-bounded control problem and yields four central results:

\begin{itemize}[itemsep=2pt]
\item an orthogonal decomposition of total misalignment into observable and dark components (Proposition~\ref{prop:pythagoras});
\item the Information Failure Law, an irreducible lower bound on total misalignment determined by the dark domain (Theorem~\ref{thm:ifl});
\item a Lyapunov based uniform ultimate boundedness guarantee under explicit adaptation dominance and bounded perturbation assumptions (Proposition~\ref{prop:uub});
\item an explicit control barrier function condition for safety invariance (Proposition~\ref{prop:safety}).
\end{itemize}

These results show that misalignment is not merely a failure of optimization or control but a structural property of systems operating under partial observability: total misalignment is bounded below by what an agent cannot see, and bounded above by how well it adapts to what it can see. KAD Theory thus offers a principled mathematical basis for designing adaptive controllers, interpretable AI systems, cognitive architectures, and governance mechanisms capable of operating safely in dynamic and uncertain environments.

\section{Future Work}

KAD Theory opens several mathematically rich and practically consequential research directions.

\begin{itemize}[itemsep=2pt]
\item \textbf{Nonlinear and time-varying extensions.} Extending the dynamics \eqref{eq:dynamics} beyond the present setting to nonlinear operators, nonlinear visibility maps, and nonlinear drift processes, including contraction-analysis and differential-geometric formulations of Theorem~\ref{thm:ifl} and Proposition~\ref{prop:uub}.
\item \textbf{High dimensional domain modeling.} Developing scalable, finite dimensional approximations of $D(t)$, $A(t)$, and $P(t)$ for complex environments such as multimodal AI systems, cyber physical infrastructures, and socio-technical networks.
\item \textbf{Empirical estimation of $c$, $\delta$, and $\rho$.} Designing estimators for the adaptation dominance constant $c$, the perturbation bound $\delta$, and the stability ratio $\rho(t)$ from observational data, enabling empirical tests of the Information Failure Law and the uniform ultimate boundedness result across engineered systems, machine learning models, human cognition, and organizational decision processes.
\item \textbf{Real time estimation of visibility operators.} Algorithms for inferring or approximating $P(t)$ online, including adaptive sensing, attention allocation, interpretability mechanisms, and governance driven oversight, directly targeting the dual control problem \eqref{eq:dualcontrol}.
\item \textbf{Applications in autonomous and organizational systems.} Applying the framework to autonomous vehicles, robotics, large scale AI systems, human machine teams, and organizational governance structures via the joint action perception architecture of Section~\ref{sec:dual}.
\end{itemize}
\newpage
\begin{thebibliography}{9}

\bibitem{khalil2002}
Khalil, H.~K. (2002). \emph{Nonlinear Systems} (3rd ed.). Prentice Hall.

\bibitem{ames2019}
Ames, A.~D., Coogan, S., Egerstedt, M., Notomista, G., Sreenath, K., \& Tabuada, P. (2019). Control Barrier Functions: Theory and Applications. In \emph{2019 18th European Control Conference (ECC)}, 3420--3431.

\bibitem{luenberger1969}
Luenberger, D.~G. (1969). \emph{Optimization by Vector Space Methods}. John Wiley \& Sons.

\bibitem{billingsley1995}
Billingsley, P. (1995). \emph{Probability and Measure} (3rd ed.). John Wiley \& Sons.

\bibitem{feldbaum1960}
Feldbaum, A.~A. (1960). Dual control theory, Parts I--IV. \emph{Automation and Remote Control}, 21.

\bibitem{bertsekas2017}
Bertsekas, D.~P. (2017). \emph{Dynamic Programming and Optimal Control}, Vol.~1 (4th ed.). Athena Scientific.

\bibitem{astrom2008}
\AA{}str\"om, K.~J., \& Murray, R.~M. (2008). \emph{Feedback Systems: An Introduction for Scientists and Engineers}. Princeton University Press.

\bibitem{amodei2016}
Amodei, D., Olah, C., Steinhardt, J., Christiano, P., Schulman, J., \& Man\'e, D. (2016). Concrete Problems in AI Safety. \emph{arXiv:1606.06565}.

\bibitem{russell2019}
Russell, S. (2019). \emph{Human Compatible: Artificial Intelligence and the Problem of Control}. Viking.

\bibitem{zafar2026}
Zafar, U. (2026). \emph{The Unified Principle of Misalignment and KAD Theory: A Cross-Domain Framework for Alignment, Perception, and Systemic Failure}. Zenodo. https://doi.org/10.5281/zenodo.18684749

\end{thebibliography}
\newpage
\appendix

\section{Tables}

\begin{table}[h]
\centering
\caption{Summary of Core Objects in KAD Theory}
\begin{tabular}{ll p{7.5cm}}
\toprule
Symbol & Name & Interpretation \\
\midrule
$D(t)$ & Domain State & True environment or task structure, $D(t)\in H$ \\
$A(t)$ & Agent State & Internal model or learned representation, $A(t)\in H_{\mathrm{obs}}(t)$ by Definition~\ref{def:representability} \\
$P(t)$ & Visibility Operator & Orthogonal projection onto $H_{\mathrm{obs}}(t)$, the observable subspace \\
$D_{\mathrm{dark}}(t)$ & Dark-Domain Component & $(I-P(t))D(t)$, the unobservable portion of the domain \\
$E(t)$ & Observable Alignment Error & $P(t)D(t)-A(t)$, correctable via adaptation \\
$\Delta(t)$ & Total Misalignment & $D(t)-A(t) = E(t)+D_{\mathrm{dark}}(t)$ \\
$W(t)$ & Domain Drift & $\dot D(t)$, exogenous change in the environment \\
$U(t)$ & Adaptation Policy & Agent's corrective update, $\dot A(t)=U(t)-\lambda A(t)$ \\
$\lambda$ & Decay Rate & Representational decay / forgetting rate \\
$\rho(t)$ & Stability Ratio & $\|U(t)\|/\|P(t)W(t)\|$ \\
\bottomrule
\end{tabular}
\end{table}

\begin{table}[h]
\centering
\caption{Stability, Boundedness, and Failure Conditions}
\begin{tabular}{l l l}
\toprule
Condition & Criterion & Outcome \\
\midrule
Assumption~\ref{ass:dominance} holds & $\langle E,U-PW\rangle\ge c\|E\|^2$, $c>0$ & UUB: $\limsup\|E(t)\|\le\delta/c$ (Prop.~\ref{prop:uub}) \\
$\rho>1$ \& directions aligned & $\|U\|>\|PW\|$, Remark~\ref{rem:rho} & Adaptation-dominant (stable phase) \\
$\rho<1$ & $\|U\|<\|PW\|$ & Drift-dominant (divergent phase) \\
$D_{\mathrm{dark}}(t)=0$ & $H_{\mathrm{obs}}(t)=H$ (full observability) & $\Delta(t)=0$ possible (Cor.~\ref{cor:full-obs}) \\
$D_{\mathrm{dark}}(t)\ne 0$ & $H_{\mathrm{obs}}(t)\subsetneq H$ & $\|\Delta(t)\|\ge\|D_{\mathrm{dark}}(t)\|>0$ (Thm.~\ref{thm:ifl}) \\
$\kappa\le c$, $\delta\le\epsilon\sqrt{c\kappa}$ & CBF condition & Safe set $S$ forward invariant (Prop.~\ref{prop:safety}) \\
\bottomrule
\end{tabular}
\end{table}

\section{Extended Derivations and Numerical Implementation}

\subsection{Discretization of the Alignment Dynamics}

For numerical experiments, the visibility operator $P(t)$ and its complement $I-P(t)$ are implemented as:
\begin{itemize}[itemsep=2pt]
\item projection matrices in finite-dimensional simulations;
\item orthogonal basis masks for high-dimensional embeddings;
\item kernel-induced projections for functional or infinite-dimensional settings.
\end{itemize}
Time-varying visibility is discretized as $P_k=P(t_k)$, with $\dot P(t_k)$ approximated by a finite difference $\dot P(t_k)\approx (P_{k+1}-P_k)/\Delta t$. Equation~\eqref{eq:dynamics} is then integrated using standard ODE solvers: explicit Euler for fast prototyping, Runge--Kutta (RK4) for stable medium-accuracy integration, and adaptive solvers (e.g.\ Dormand--Prince) for stiff or rapidly varying drift processes \cite{astrom2008}. Stability of the discretization is sensitive to $\Delta t$, particularly when $\dot P(t)D(t)$ is large.

\subsection{Estimating Assumption~\ref{ass:dominance} and Assumption~\ref{ass:bounded} Empirically}

Given a time series of $E(t)$, $U(t)$, $P(t)W(t)$, $A(t)$, and an estimate of $\dot P(t)$, the constant $c$ in Assumption~\ref{ass:dominance} can be estimated as the largest $c$ such that $\langle E(t),U(t)-P(t)W(t)\rangle \ge c\|E(t)\|^2$ holds across the observed trajectory (e.g.\ via the empirical minimum of the ratio $\langle E,U-PW\rangle/\|E\|^2$ over windows where $\|E(t)\|$ is bounded away from zero). Similarly, $\delta$ in Assumption~\ref{ass:bounded} is estimated as $\sup_t\|\lambda A(t)+\dot P(t)D(t)\|$. These estimates allow direct empirical evaluation of the bound $\delta/c$ in Proposition~\ref{prop:uub} and the safety conditions of Proposition~\ref{prop:safety}.

\subsection{Reproducibility}

Experiments validating KAD Theory should specify: random seeds and initialization procedures for $D_0,A_0,P_0$; simulation time horizons and step sizes; the drift model used for $W(t)$ (e.g.\ Gaussian drift, adversarial perturbations, or structured regime shifts); the adaptation policy $U(t)$ (gradient-based update, control law, or learned policy); and the visibility schedule for $P(t)$ (fixed projection, periodic sensing, or adaptive attention mechanism). Reporting estimated values of $c$, $\delta$, $\rho(t)$, and $\|D_{\mathrm{dark}}(t)\|$ alongside the realized trajectory of $\|E(t)\|$ and $\|\Delta(t)\|$ allows direct comparison with the theoretical bounds of Proposition~\ref{prop:uub}, Corollary~\ref{cor:total-bound}, and Proposition~\ref{prop:safety}.

\end{document}
