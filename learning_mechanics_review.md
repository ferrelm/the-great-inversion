# Toward a Physics of Learning: The Emerging Scientific Theory of Deep Learning and Large Language Models

**A Review Article**

---

> *"The same empirical tension that drove the development of classical mechanics — nature presenting something interesting we cannot predict or explain — is present in deep learning. We should approach this task as scientists."*
> — Simon et al. (2026)

---

## Abstract

Deep learning has produced the most consequential technological transformation of our era, yet it rests on a scientific foundation of striking fragility. Neural networks are trained by methods discovered largely through trial and error; their emergent properties are catalogued empirically rather than derived from first principles; and large language models (LLMs) exhibit capabilities — and failures — that remain profoundly opaque to their creators. This review surveys the evidence that this situation is changing. We argue, following Simon et al. (arXiv:2604.21691, 2026) and a growing body of theoretical work, that a coherent scientific theory of deep learning — which those authors propose to call *learning mechanics* — is beginning to crystallize around five converging research programs: analytically solvable model systems, tractable mathematical limits, empirical macroscopic laws, theories of hyperparameters, and the discovery of universal behaviors across architectures and tasks. We situate this program relative to its intellectual neighbors — the neural tangent kernel framework, neural scaling laws, mechanistic interpretability, singular learning theory, and the physics of deep learning — and present an extended discussion of the most important open problems, including a theory of in-context learning and emergence, scaling laws for downstream tasks, theories of alignment and fine-tuning, the role of data geometry, and the long-term prospect of connecting learning mechanics to the neurosciences. The picture that emerges is that of a science at the stage of thermodynamics circa 1820: robust empirical regularities have been established, promising theoretical frameworks have appeared, but the unifying Newton has not yet arrived.

---

## 1. Introduction: The Black Box Paradox

The history of science contains many examples in which powerful technologies preceded their own theoretical understanding. The steam engine was built and refined for a century before thermodynamics existed to explain it; heavier-than-air flight preceded aerodynamic theory; and antibiotics were used for decades before the mechanisms of bacterial resistance were mapped. In each case, the technology functioned — it was useful, it could be engineered, it could even be improved — but it could not be *predicted* from first principles, and its engineers operated with a mixture of empirical intuition and accumulated craft knowledge rather than science.

Deep learning occupies precisely this position today. Neural networks solve problems of extraordinary difficulty — recognizing images, translating between languages, generating coherent and factual long-form text, reasoning through multi-step mathematical arguments, predicting protein structure — and they do so with performance that often exceeds the best human specialists. Yet the theoretical basis for these successes is, by any rigorous standard, almost entirely absent. We do not know from first principles which architectures will work. We do not know how to set hyperparameters without large-scale empirical sweeps. We do not know why scale works as well as it does, nor precisely when it will stop working. We do not know why networks generalize despite massive overparameterization, nor what the networks have actually *learned* in any deep sense.

This is not for lack of effort. Theoretical investigations of neural networks stretch back to the perceptron (Rosenblatt, 1958) and the early attempts to characterize the expressive power of shallow networks (Cybenko, 1989; Hornik et al., 1989). Classical learning theory — VC dimension, PAC learning, Rademacher complexity — provided clean, rigorous guarantees for simple models, but these guarantees grew increasingly vacuous as networks grew larger. The rise of deep learning through multilayer networks, backpropagation (Rumelhart et al., 1986), and increasing scale exposed the limits of the classical toolkit: overparameterized networks that should, according to classical wisdom, overfit catastrophically instead generalize beautifully (Zhang et al., 2017). The classical presumption that optimization would be hard in the presence of non-convex loss landscapes turned out to be empirically wrong for networks of sufficient width (Goodfellow et al., 2015; Choromanska et al., 2015). Explanations lagged reality at every turn.

The theoretical response to this situation has, over the past decade, gradually shifted in character. The early post-deep-learning period was dominated by attempts to patch classical frameworks — tighter generalization bounds, new notions of complexity, PAC-Bayes analyses. These efforts have produced genuine insights but have not delivered a unified predictive framework. More recently, a different orientation has taken hold: rather than asking what classical theory can say about deep learning, researchers have begun studying deep learning empirically *as scientists*, seeking the regularities, universal laws, and mechanistic explanations that are the raw material of any mature scientific field (Olshausen & Field, 1996; Saxe et al., 2014; Raghu et al., 2017; Bahri et al., 2020).

It is this second tradition — the scientific rather than mathematical tradition in deep learning theory — that is the subject of this review. Our central thesis, which we develop and defend in detail, is that this tradition is converging on a coherent theoretical framework. We call this framework *learning mechanics*, following the proposal of Simon et al. (2026) in their landmark synthesis paper "There Will Be a Scientific Theory of Deep Learning" (arXiv:2604.21691). The evidence for this convergence comes from five lines of research that we review in turn, before turning to the relationship between learning mechanics and its intellectual neighbors, to honest responses to the main skeptical arguments, and finally to an extended treatment of the most important open problems.

We write for an audience that includes physicists and mathematically trained non-specialists, and we have accordingly adopted a style that favors conceptual clarity over technical density. Formal results are stated without proof; pointers to the primary literature are given throughout.

---

## 2. Historical Background: From Classical Learning Theory to Learning Mechanics

### 2.1 The Classical Program and Its Limits

Statistical learning theory, as developed by Vapnik and Chervonenkis (1971), Valiant (1984), and their successors, provided the first rigorous framework for understanding when learning from finite samples can generalize to unseen data. The core object of study was the generalization gap — the difference between training error and test error — and the central insight was that this gap is controlled by a measure of the *complexity* of the hypothesis class. The VC dimension, Rademacher complexity, and related notions all capture, in different ways, how many distinct functions a model class can implement, and thus how many data points are needed to pin down which of those functions is the correct one.

For the models that classical theory was built around — linear classifiers, support vector machines, shallow neural networks — these frameworks worked beautifully. The theory delivered tight bounds, guided model selection, and provided a clean conceptual picture of the bias-variance tradeoff. But deep learning broke this picture in a fundamental way. Modern neural networks are massively overparameterized: they have orders of magnitude more parameters than training samples, and according to classical theory they should therefore be extraordinarily prone to overfitting. Instead, they generalize remarkably well (Zhang et al., 2017). The classical complexity measures — which diverge with model size — predict vacuous bounds for any practical network.

This failure was not merely quantitative. It pointed to a qualitative gap: classical theory measured complexity of hypothesis classes, but was largely silent about the *dynamics* of how networks actually find solutions within those classes. The optimizer — stochastic gradient descent and its variants — has strong implicit biases that are not captured by complexity measures alone. Understanding generalization in deep networks requires understanding what kinds of solutions SGD prefers, not just what solutions are in principle representable.

### 2.2 The Statistical Physics Tradition

A parallel tradition — the statistical physics of learning — has existed since the 1980s, associated with names like Hopfield (1982), Amit et al. (1985), Gardner and Derrida (1988), and the subsequent replica-method analyses of learning curves in neural networks (Seung et al., 1992; Watkin et al., 1993). This tradition adopted the physicist's approach: study typical, average-case behavior rather than worst-case bounds; exploit the simplifications that arise in large-dimensional limits; and use the tools of statistical mechanics — mean-field theory, replica methods, random matrix theory — to derive exact results in solvable model systems.

This program produced beautiful results for simple models: the storage capacity of the Hopfield network, the learning curve of the perceptron, the generalization error of linear classifiers in high dimensions. But it struggled to engage directly with the phenomena driving modern deep learning — multilayer networks, gradient-based training, the transformer architecture — because the analytical tools required to handle these systems were not available. The statistical physics tradition contributed crucial *methods* and *conceptual vocabulary*, but its direct outputs remained far from the empirical frontier.

### 2.3 The Transition: Deep Learning as Empirical Science

The theoretical situation began to shift around 2015–2018, driven by several converging developments. First, the empirical puzzles of deep learning became sufficiently sharp and reproducible that they attracted sustained theoretical attention: the double descent phenomenon (Belkin et al., 2019), the surprisingly good optimization of non-convex loss landscapes (Goodfellow et al., 2015), the structured representations learned by deep networks (Zeiler & Fergus, 2014). Second, new analytical tools became available, particularly the neural tangent kernel (Jacot et al., 2018) and the mean-field theory of deep networks (Poole et al., 2016; Schoenholz et al., 2017), which made exact computation possible in certain limits. Third, and perhaps most importantly, a cultural shift occurred in which a significant fraction of the theoretical community adopted an explicitly *empirical-scientific* methodology: identify a precise, reproducible phenomenon; characterize it quantitatively; search for mechanistic explanations; make falsifiable predictions.

It is from this third shift that the emerging science of learning mechanics is born. As Simon et al. (2026) observe, the change was from a largely mathematical study of what is possible to a truly scientific effort to describe, explain, and ultimately predict the behavior of complex empirical systems. The analogy to the development of classical physics is apt: just as physics moved from Aristotelian qualitative description to Galilean quantitative experiment and Newtonian first-principles theory, deep learning theory is moving from heuristics and qualitative intuitions to precise empirical laws and, tentatively, to principled derivations.

---

## 3. Five Pillars of Learning Mechanics

The case for an emerging scientific theory of deep learning rests on five converging bodies of work. We describe each in turn, emphasizing both what has been established and where the key uncertainties lie.

### 3.1 Analytically Solvable Settings

Every mature branch of physics maintains a library of exactly solvable models — the harmonic oscillator, the hydrogen atom, the Ising model — that serve as theoretical laboratories. These are not realistic descriptions of any system of practical interest, but they are valuable precisely because they can be analyzed with complete rigor, revealing structural insights that generalize to more complex cases. Learning mechanics is building a similar library.

**Deep linear networks** — networks with multiple layers but no nonlinearity — are perhaps the most important example. Despite their apparent triviality (they compute only linear functions), they exhibit surprisingly rich training dynamics that can be computed exactly. Saxe et al. (2014) showed that gradient descent in deep linear networks produces a sequence of singular value decompositions, providing the first exact description of how structured representations emerge during training. This analysis illuminated the mechanisms behind incremental learning and the role of network depth in shaping the trajectory of learning, with implications that extend to nonlinear networks.

**Teacher-student models**, in which a student network is trained to reproduce the input-output function of a teacher network of known structure, provide another class of exactly solvable systems (Seung et al., 1992; Goldt et al., 2020). In high-dimensional limits, the learning dynamics of these models can be characterized by a small system of ordinary differential equations describing the evolution of a few summary statistics — overlaps between student and teacher weights — rather than the full parameter trajectory. This dimensional reduction is precisely the kind of coarse-graining that a macroscopic theory of learning should aspire to.

More recently, exactly solvable models have been constructed for in-context learning (Mahankali et al., 2023; Ahn et al., 2023), demonstrating that a single layer of self-attention can implement gradient descent in its forward pass — a remarkable result connecting the behavior of transformers to classical optimization theory.

The key lesson from solvable settings is methodological: structural insights first revealed in idealized systems often transfer to realistic ones, even when the specific calculations do not. The gradient-descent-as-optimization analogy for in-context learning, for instance, was discovered in simplified models but provides conceptual guidance for understanding LLMs.

### 3.2 Tractable Limits and the Lazy/Rich Dichotomy

A second pillar consists of mathematical limits that make deep networks tractable by sending some architectural parameter — width, depth, or a combination — to infinity. The most influential of these is the infinite-width limit, whose study led directly to the neural tangent kernel (NTK) framework.

**The Neural Tangent Kernel.** Jacot, Gabriel, and Hongler (2018) showed that as the width of a neural network is sent to infinity with standard random initialization, the network's training dynamics simplify dramatically. In this limit, the network function evolves according to a linear differential equation in function space, and the kernel governing this evolution — the NTK — converges to a deterministic, time-independent limit. This means that an infinitely wide network behaves like a linear model trained with a fixed kernel, and its generalization properties can be analyzed using the classical theory of kernel methods. In particular, Jacot et al. gave the first general proof of convergence of wide networks to a global minimum, and characterized the limiting spectrum of the Hessian of the loss throughout training.

The NTK framework was a conceptual breakthrough: it showed that the infinite-width limit makes neural network training mathematically tractable, and it provided the first principled connection between deep learning and classical statistical learning theory (in the form of kernel methods). Subsequent work extended the NTK to finite-width corrections (Hanin & Nica, 2019; Huang & Yau, 2019) and to convolutional architectures (Arora et al., 2019).

**The Lazy/Rich Dichotomy.** However, the NTK limit also revealed its own limitations. In the infinite-width regime, network weights barely move from their initialization — this is the *lazy* training regime. Feature learning — the process by which internal representations adapt to the task during training — is absent. This is a serious deficiency: the practical success of deep learning is widely believed to depend critically on the ability of networks to learn task-relevant features, and the most powerful LLMs have clearly learned rich, structured representations not present at initialization.

This observation motivated the identification of the *rich* or *feature-learning* regime, in which network weights evolve significantly from initialization and representations change substantially during training. The distinction between lazy and rich training is now understood to be a genuine qualitative dichotomy, not merely a quantitative one, and a major theoretical effort has been devoted to characterizing the rich regime.

**Maximal Update Parametrization.** The most influential resolution of the lazy/rich tension is the Maximal Update Parametrization (μP), introduced by Yang and Hu (2020, 2021) and further developed in Yang et al. (2022). μP is a prescription for how to initialize weights and scale learning rates as a function of network width, designed so that all layers make updates of the same order of magnitude — and in particular, so that feature learning is preserved in the infinite-width limit. The key practical consequence is striking: under μP, the optimal learning rate is *width-independent*, which means that hyperparameters tuned on a small proxy model can be transferred directly to a much larger one. This has been validated across a range of architectures, including GPT-3 class LLMs, and represents perhaps the most practically consequential direct output of deep learning theory to date.

**Joint Scaling Limits.** A further line of work studies what happens when both width and depth are sent to infinity simultaneously, at different rates. Bordelon and Pehlevan (2022, 2023) and Yang et al. (2023) have characterized "depth-μP" for residual networks, while other authors have studied the distinction between different scaling regimes (the "mean-field" limit vs. the NTK limit vs. the μP limit) using the language of tensor programs (Yang, 2019–2022). This body of work is beginning to describe the full phase diagram of neural network learning as a function of architectural and training parameters — an important step toward a comprehensive mechanics.

### 3.3 Empirical Macroscopic Laws

A third pillar consists of simple empirical laws that describe the macroscopic behavior of neural networks without requiring detailed microscopic understanding. The existence and robustness of such laws is itself evidence that a theory is possible: nature does not produce clean empirical regularities by accident.

**Neural Scaling Laws.** The most famous such laws are the neural scaling laws (Hestness et al., 2017; Kaplan et al., 2020; Hoffmann et al., 2022). The key empirical finding is that the test loss of a neural language model decreases as a power law in model size, training data, and compute:

$$L(N, D) \approx L_\infty + \left(\frac{N_c}{N}\right)^{\alpha_N} + \left(\frac{D_c}{D}\right)^{\alpha_D}$$

where $N$ is the number of parameters, $D$ the number of training tokens, and the exponents $\alpha_N, \alpha_D$ are typically of order 0.05–0.1, remarkably consistent across architectures, modalities, and tasks (Kaplan et al., 2020; Henighan et al., 2020; Zhai et al., 2022). This regularity is extraordinary: it means that the performance of a model with 100 billion parameters on a dataset of one trillion tokens can be *predicted* from experiments with models 100–1000 times smaller, with errors of a few percent. Hoffmann et al. (2022) — the Chinchilla paper — used these laws to derive optimal model/data trade-offs for a given compute budget, with direct practical consequences for LLM training.

Theoretical understanding of *why* scaling laws exist, and what determines the exponents, has been a major research target. Bahri et al. (2021) connected power-law scaling to the intrinsic dimensionality of the data manifold and kernel regression. Bordelon, Atanasov, and Pehlevan (2024) derived scaling laws from a dynamical model of learning, obtaining them as solutions to a tractable system of mean-field equations — a significant step toward a first-principles derivation. The important theoretical work remains: a complete derivation of scaling law exponents from the architecture and data distribution, and an understanding of when and why power laws break (Cabannes et al., 2023).

**The Edge of Stability.** A second major empirical law governs the *training dynamics* of gradient descent. Cohen et al. (2021) demonstrated, in what has become one of the most influential empirical results in deep learning theory, that full-batch gradient descent on neural network training objectives systematically operates in a regime they called the *Edge of Stability*. The key observation is that the maximum eigenvalue of the training loss Hessian — the *sharpness* — undergoes a period of *progressive sharpening*, rising monotonically during training until it reaches the value $2/\eta$, where $\eta$ is the learning rate. It then stabilizes at, or slightly above, this threshold for the remainder of training, while the loss continues to decrease, albeit non-monotonically over short timescales.

This phenomenon is inconsistent with the standard analysis of gradient descent, which requires the sharpness to remain *below* $2/\eta$ for the descent lemma to hold. The edge of stability implies that practical neural network training operates in a regime where classical optimization theory fails, and that the actual training dynamics must involve subtle implicit regularization effects. Understanding the edge of stability mathematically has become a major theoretical challenge, with contributions from bifurcation theory (Song & Yun, 2023), self-stabilization mechanisms (Damian et al., 2023), and higher-order flow approximations (Cohen et al., 2025). The stochastic version of the phenomenon — the "edge of stochastic stability" (Andreyev & Beneventano, 2024) — shows that mini-batch SGD operates at an analogous threshold, with important implications for the role of batch size in generalization.

**Coarse Properties of Representations and Weights.** Additional empirical laws govern the structure of neural network weights and representations at convergence. The singular value spectra of weight matrices obey characteristic distributions (Martin & Mahoney, 2021); the effective rank of representations varies systematically across layers and scales (Roy & Vetterli, 2007; Huh et al., 2021); and the loss landscape near converged solutions has a characteristic geometry related to the sharpness. These laws provide quantitative observables for a macroscopic theory to target.

### 3.4 Theories of Hyperparameters

One of the most practically painful aspects of deep learning is the need for extensive hyperparameter tuning: learning rate, batch size, weight decay, momentum, dropout, architectural choices. These are chosen largely through grid search and intuition, consuming enormous computational resources. A fourth pillar of learning mechanics is the program to replace this empiricism with principled theory.

We have already discussed μP as a theory of the learning rate. More broadly, the μP framework — instantiated in the "Tensor Programs" series of Yang (2019–2022) — provides a principled treatment of *all* hyperparameters that affect the scale of parameter updates, yielding scaling rules for initialization, learning rate, and learning rate scheduling that are stable under width increase. The key insight is that by choosing parametrization carefully, the effective dynamics of a wide network become width-independent, making small-scale proxy models informative for large-scale ones.

Beyond μP, progress has been made on understanding learning rate schedules (Defazio et al., 2023), the effects of weight decay and its relationship to implicit regularization (Blanc et al., 2020), and the role of batch size in controlling the noise in gradient estimates and its effects on generalization (Smith et al., 2018). The goal of "disentangling" hyperparameters — finding parametrizations in which each hyperparameter has a well-defined, architecture-independent meaning — is central to the learning mechanics program.

### 3.5 Universal Phenomena

The fifth and perhaps conceptually richest pillar is the discovery of *universal* behaviors that appear across architectures, datasets, and tasks. Universality is a hallmark of deep theoretical understanding in physics: the universality classes of phase transitions, for instance, tell us that systems as different as water and iron magnets behave identically near their critical points, because critical behavior depends only on symmetry and dimensionality, not on microscopic details. The discovery of analogous universality in deep learning is evidence that a similarly deep explanatory framework may be within reach.

**Grokking.** One of the most striking universal phenomena is grokking, discovered by Power et al. (2022). Training a small transformer on algorithmic tasks (modular arithmetic, permutation composition), one observes that the network achieves perfect training accuracy quickly, but test accuracy remains near chance for hundreds of thousands of additional steps. Then, suddenly, test accuracy rises to near-perfect. This "delayed generalization" — the network transitions from memorization to genuine understanding long after training loss has converged — appears across architectures and tasks. Mechanistic interpretability of the grokked models revealed (Nanda et al., 2023) that grokking corresponds to the gradual amplification of structured algorithmic circuits — for modular addition, these circuits implement a discrete Fourier transform followed by a trigonometric calculation — and the subsequent suppression of the memorization mechanisms. Grokking is thus a phase transition in representation: the network discovers the correct algorithm but does not deploy it until a long implicit regularization process has been completed. Liu et al. (2022) provided an effective theory of representation learning that captures the key dynamics.

**The Platonic Representation Hypothesis.** At a very different scale, Huh, Cheung, Wang, and Isola (2024) documented a striking convergence of internal representations across neural networks trained on different data modalities, with different architectures and objectives. Measuring representational similarity via kernel alignment and mutual nearest-neighbor analysis, they found that as vision models and language models scale up, the distances between datapoints in their respective representation spaces become increasingly aligned. They proposed that this convergence is a selective pressure toward a shared "platonic representation" — a statistical model of the world that any sufficiently capable model converges toward, regardless of its particular architectural or training history. The philosophical resonance with Plato's ideal forms is acknowledged, though the empirical claim is precisely quantified: alignment between vision and language model representations increases monotonically with model scale (Huh et al., 2024).

**Universality in Inductive Biases.** A related line of work has documented that networks with very different architectures often discover similar algorithms or representations when trained on the same tasks (Li & Liang, 2016; Kornblith et al., 2019). This suggests that the effective inductive biases of large, well-trained networks are not primarily determined by architectural details, but by more fundamental properties of the training objective and the data. What these fundamental properties are — whether they are related to the implicit bias of gradient descent, to properties of the data distribution, or to some deeper informational structure — is an important open question.

---

## 4. Learning Mechanics and Its Intellectual Neighbors

The learning mechanics program does not exist in isolation. It has important relationships with several adjacent research programs, which we briefly characterize here.

### 4.1 The Statistical and Information-Theoretic Perspectives

Classical statistical learning theory (Vapnik & Chervonenkis, 1971; Valiant, 1984) seeks generalization bounds that hold with high probability over the choice of training data, for *any* learning algorithm. This worst-case focus produces rigorous but often vacuous bounds for deep networks. Generalization in deep networks appears to require average-case rather than worst-case analysis, and sensitivity to the specific algorithm (SGD and its variants) rather than the hypothesis class alone.

Information-theoretic approaches (Tishby & Schwartz-Ziv, 2017; Kolchinsky et al., 2019) have proposed that deep networks can be understood as performing information compression, and that the information bottleneck principle may explain their generalization. While these ideas have generated important debates (Saxe et al., 2018; Goldfeld & Polyanskiy, 2020), the information-theoretic perspective has not yet produced a predictive quantitative framework for realistic networks.

### 4.2 Physics of Deep Learning

A growing community of physicists has approached deep learning using tools from statistical mechanics, random matrix theory, and field theory (Bahri et al., 2020; Roberts, Yaida, & Hanin, 2022). The "Principles of Deep Learning Theory" (Roberts et al., 2022) derives systematic corrections to the NTK using perturbative field theory, while mean-field analyses have characterized the propagation of signals and gradients through deep networks at initialization (Poole et al., 2016; Schoenholz et al., 2017). Random matrix theory has proven particularly valuable for understanding the spectra of weight matrices and Hessians (Pennington & Wakhloo, 2017; Martin & Mahoney, 2021). Learning mechanics inherits and extends this tradition.

### 4.3 Singular Learning Theory

Singular learning theory (SLT), developed over 25 years by the Japanese mathematician Sumio Watanabe (Watanabe, 2009, 2018), provides a rigorous Bayesian framework for statistical models with non-identifiable parameterizations — models in which many different parameter values produce the same function. Neural networks are conspicuously singular in this sense, due to permutation symmetries (permuting neurons in a layer does not change the function), scaling symmetries (rescaling weights between layers), and the degeneracies introduced by non-injective activation functions (Murfet et al., 2022).

The central object of SLT is the *real log canonical threshold* (RLCT), a geometric invariant that generalizes the notion of effective dimension to singular models. In regular models, generalization error scales as $d/(2n)$ where $d$ is the parameter count and $n$ is the sample size; in singular models, it scales as $\lambda/n$ where $\lambda = \text{RLCT} \leq d/2$, reflecting the fact that the geometry of singular parameter spaces is richer than simple dimension suggests. SLT also provides the WBIC (Widely Applicable Bayesian Information Criterion), a model selection criterion valid for singular models.

The "developmental interpretability" program (Murfet et al., 2022; Lau et al., 2023) aims to apply SLT to understand the phase transitions in neural network training — the discrete jumps in structure and capability that occur during training and that may underlie phenomena like grokking and emergent capabilities. SLT provides a natural language for these transitions in terms of changes in the RLCT as the network moves through different regions of parameter space. This program is at an early stage — computing RLCT for realistic large networks remains a major open problem — but it offers a potentially powerful framework for understanding the qualitative dynamics of training.

### 4.4 Mechanistic Interpretability

Mechanistic interpretability (MI) (Olah et al., 2020; Elhage et al., 2021; Olsson et al., 2022) aims to reverse-engineer the specific algorithms implemented by neural networks: to identify the circuits — networks of interacting attention heads, MLPs, and residual connections — that implement specific computations, and to understand these circuits in human-readable terms.

Key results in mechanistic interpretability include the identification of *induction heads* — pairs of attention heads that together implement a pattern-matching algorithm central to in-context learning (Olsson et al., 2022); the mechanistic characterization of how transformers implement indirect object identification (Wang et al., 2022); and the discovery that networks represent more features than they have neurons by "superimposing" feature representations in the same dimensional space (Elhage et al., 2022).

Simon et al. (2026) articulate a symbiotic relationship between learning mechanics and mechanistic interpretability: if learning mechanics aspires to be the *physics* of deep learning — describing coarse-grained statistics and macroscopic laws — then mechanistic interpretability aspires to be its *biology*, describing the specific algorithms and circuits that implement these macroscopic behaviors. The physics provides the context within which biological mechanisms arise; biology constrains and motivates the physics. In the natural sciences, physics and biology are mutually informing, and there is good reason to expect an analogous relationship between learning mechanics and mechanistic interpretability.

---

## 5. Honest Skepticism and Responses

The case for learning mechanics must engage seriously with skeptical arguments. Simon et al. (2026) identify five major objections, which we discuss and evaluate.

**"Theory has been tried for decades. If there were a theory, we would have found it."** This objection conflates the difficulty of the problem with its impossibility. Statistical mechanics took more than a century to go from Bernoulli's kinetic theory to the rigorous treatment of Gibbs; quantum mechanics required 25 years of intense confusion before Heisenberg and Schrödinger; even classical mechanics had a long pre-Newtonian period. The failure of previous theoretical programs tells us that the problem is hard, not that it is insoluble. Crucially, the theoretical tools available in 2025 — random matrix theory, mean-field theory for deep networks, the NTK framework, tensor programs, singular learning theory, and the vastly improved empirical infrastructure of modern ML — are qualitatively different from what was available in 2005.

**"LLMs are too complex for first-principles understanding."** This objection confuses *first-principles* with *microscopic*. Thermodynamics is a first-principles theory of systems containing $10^{23}$ molecules, yet it makes no reference to the individual dynamics of any molecule. Learning mechanics does not aspire to predict the behavior of every neuron; it aspires to identify the right *level of resolution* — the coarse-grained observables, the macroscopic statistics — at which principled predictions become possible. The existence of neural scaling laws, which predict the performance of 100-billion-parameter models from 1-billion-parameter experiments, shows that such coarse-grained predictability is not a fantasy.

**"We need a theory of data, not of networks."** This is partially correct: data structure is a crucial and understudied element. But it is not a reason to abandon the study of network dynamics; it is a reason to pursue it jointly with the study of data geometry. A theory of how networks interact with data is precisely what learning mechanics aims to be.

**"What matters is high-level behavior, not microscopic theory."** This conflates different levels of description. The practical importance of theory lies not in microscopic detail but in *predictive principles* — rules that guide model design, reduce the need for hyperparameter sweeps, and predict failure modes before they occur. The μP framework is a perfect example: it is a theoretical result, derived from careful analysis of infinite-width limits, that has direct practical consequences for LLM training at scale.

---

## 6. Open Problems and Research Frontiers

This is the most important section of the review. The argument of the preceding sections is that learning mechanics is an *emerging* science — one in which the foundational structures are becoming visible but the edifice remains far from complete. We identify and develop the most important open problems, organized by theme.

### 6.1 A Theory of Emergent Capabilities and In-Context Learning

Perhaps the deepest mystery in the current era of LLMs is the emergence of qualitatively new capabilities as model scale increases. Wei et al. (2022) documented numerous examples of *emergent* abilities — capabilities that are essentially absent in smaller models and appear suddenly, with roughly discontinuous scaling, in larger ones. Chain-of-thought reasoning, multi-step arithmetic, and certain complex reasoning tasks all appear to exhibit this pattern. Emergent abilities are scientifically puzzling because they suggest that there is no simple interpolation from small-scale experiments to large-scale behavior: the macroscopic observables are non-monotone in model size, at least for these downstream tasks.

Whether emergence reflects genuine discontinuities in capability or artifacts of measurement scales and evaluation methods is itself an active debate (Schaeffer et al., 2023). But even if emergence is partly illusory, a full theory must account for the wide variation in scaling behavior across tasks — why some capabilities scale smoothly and others exhibit apparent phase transitions.

In-context learning (ICL) — the ability of a pretrained LLM to perform new tasks from a handful of examples in the context window, without any gradient update — is the most striking and theoretically interesting emergent capability. Theoretical work has made progress by studying ICL in solvable settings. Akyürek et al. (2022) and Von Oswald et al. (2022) showed that a single transformer layer can implement gradient descent in its forward pass, providing a mechanistic explanation for why ICL resembles few-shot learning. Xie et al. (2022) showed that ICL can be understood as Bayesian inference over a prior on tasks. More recently, work on the dynamics of ICL — how and when it emerges during training — has revealed that it can be *transient* (Singh et al., 2023; Reddy, 2024): a model may learn to perform ICL during training and then lose this capability as in-weights learning (IWL) comes to dominate. Understanding the conditions under which ICL is stable versus transient, and how these conditions depend on data distribution and model scale, is an important open problem.

A full theory of ICL would ideally derive, from first principles, the conditions under which ICL emerges, the algorithms that implement it, the relationship between ICL and the task/data distribution, and the conditions under which emergent capabilities are sharp versus gradual. This requires connecting the macroscopic description (scaling laws, phase diagrams) with the mechanistic description (circuits, attention patterns), and represents one of the central open challenges for learning mechanics.

### 6.2 Downstream Scaling Laws and Task Prediction

The scaling laws of Kaplan et al. (2020) and Hoffmann et al. (2022) describe the scaling of *pretraining loss* — a smooth, aggregate measure of language model performance. But the downstream task performance that practitioners actually care about is far more complex. Individual tasks exhibit a rich variety of scaling behaviors: some scale smoothly, others plateau, some exhibit U-shaped or non-monotone behavior (inverse scaling), and some appear to scale sharply at specific compute thresholds (Srivastava et al., 2022; McKenzie et al., 2023).

Predicting downstream task performance from pretraining loss, model scale, and architecture — the "downstream scaling law" problem — is both practically important and theoretically deep. It requires understanding the relationship between the compressed aggregate statistics captured by pretraining loss and the fine-grained task-specific capabilities that depend on the specific structure of learned representations. Broken Neural Scaling Laws (Cabannes et al., 2023) have documented the conditions under which simple power laws fail, while neural neural scaling law approaches (Bansal et al., 2022, and recent work) attempt to learn predictive models of scaling behavior directly from data. A principled theory remains elusive.

This problem is intimately connected to the emergence debate: understanding when and why capabilities scale non-smoothly would, if solved, likely provide a theory of emergent abilities as a special case.

### 6.3 Theories of Fine-Tuning, RLHF, and Alignment

Modern LLMs are not just pretrained; they are fine-tuned using reinforcement learning from human feedback (RLHF) (Christiano et al., 2017; Ziegler et al., 2019; Ouyang et al., 2022) and various instruction-following methods. The theoretical understanding of fine-tuning is far behind the understanding of pretraining. Key open questions include:

- What is preserved and what is overwritten during fine-tuning? The phenomenon of *catastrophic forgetting* (McCloskey & Cohen, 1989) suggests that naive fine-tuning can destroy capabilities acquired during pretraining, yet in practice modern fine-tuning methods are remarkably effective at preserving general capabilities while acquiring new ones. Understanding the conditions for this requires a theory of how information is organized in LLM weights.

- What is the implicit bias of RLHF? Fine-tuning with RLHF optimizes a reward model derived from human preferences; understanding whether this process produces models that are genuinely aligned with human values or merely optimized to produce reward-seeking surface behaviors is a fundamental open problem with both theoretical and safety implications.

- Can a first-principles theory of alignment be built from learning mechanics? Kenton et al. (2021) and related work argue that the key question is whether a model has a sufficiently faithful world model and a good utility function. SLT offers one potential language for this: the RLCT as a measure of the effective complexity of the world model embedded in a network's weights. But connecting SLT's geometric language to the behavioral properties that alignment researchers care about remains to be done.

### 6.4 The Role of Data Geometry and Data Selection

A persistent gap in current theoretical frameworks is an adequate treatment of data. Most theoretical analyses — NTK, mean-field theory, scaling law derivations — model data as random inputs drawn from a known distribution, typically Gaussian or drawn from a low-dimensional manifold. Real training data — natural images, natural language text — has a vastly richer structure than these models capture, and there is strong reason to believe that this structure is central to the capabilities of LLMs.

Several important questions remain open. What properties of the pretraining data distribution determine scaling law exponents? The derivations of Bahri et al. (2021) and Bordelon et al. (2024) attribute the exponents to the intrinsic dimensionality and spectral properties of the data manifold — but these properties are not directly measurable for internet-scale text corpora. How does data *quality* affect learning, as distinct from quantity? Recent work on data curation (Penedo et al., 2023; Li et al., 2024) suggests that quality filters can dramatically improve sample efficiency, but a theoretical framework for "data quality" from the perspective of learning mechanics does not exist.

Understanding the relationship between data structure and representation geometry — including the theoretical basis for the Platonic Representation Hypothesis — requires developing new mathematical tools for characterizing the geometry of high-dimensional natural data distributions.

### 6.5 A Full Theory of the Loss Landscape and Optimization

The loss landscapes of deep networks are non-convex in a high-dimensional space, and their properties — the density and structure of local minima, the geometry of saddle points, the barriers between regions — determine how gradient descent navigates and what solutions it finds. Early work suggested that local minima were not problematic in practice (Dauphin et al., 2014; Goodfellow et al., 2015), but a complete theory of why gradient descent finds good solutions, and which good solutions it prefers, remains lacking.

The edge-of-stability phenomenon (Cohen et al., 2021) reveals that the standard first-order analysis of gradient descent is inadequate for neural networks. Current explanations in terms of bifurcation theory (Song & Yun, 2023), self-stabilization (Damian et al., 2023), and central flows (Cohen et al., 2025) are promising but each handles only aspects of the full picture. A unified theory of gradient descent dynamics in neural networks — one that explains progressive sharpening, edge-of-stability, the long-run descent of the loss, and the implicit regularization that drives toward generalizing solutions — would be a major advance.

Closely related is the problem of understanding *implicit regularization*: the tendency of gradient descent with small learning rates and early stopping to find solutions with specific properties (minimum norm, maximum margin, low effective rank) even in the absence of explicit regularization terms. This has been characterized for linear and shallow models (Gunasekar et al., 2017; Soudry et al., 2018; Ji & Telgarsky, 2019); generalizing these results to deep nonlinear networks is an active and important open problem.

### 6.6 Connections to Neuroscience and Biological Intelligence

One of the most scientifically exciting long-term possibilities of learning mechanics is that the principles it uncovers may illuminate not just artificial but biological neural networks. Artificial neural networks were, of course, originally inspired by the brain, but the relationship has become asymmetric: biological neuroscience has contributed relatively little to modern deep learning, while deep learning systems have become some of the best predictors of neural activity in sensory cortex (Yamins & DiCarlo, 2016; Schrimpf et al., 2020).

Several specific connections between learning mechanics and neuroscience are already visible. The lazy/rich dichotomy maps roughly onto the distinction between cortical systems that perform fixed computations (early sensory areas) and those that adapt to context and task demands (prefrontal cortex). The universality of representations documented by the Platonic Representation Hypothesis, and the analogous finding that deep network representations align with neural population codes in visual cortex (Kornblith et al., 2019), suggest that there may be deep principles — related to the geometry of natural data and the efficiency of computation — that govern representation learning in both artificial and biological systems.

Developing this connection rigorously requires both more realistic models of neural network learning (incorporating architectural constraints inspired by neuroscience, like sparsity, local learning rules, and biologically plausible optimization) and more sophisticated analyses of neural population codes in the brain. The Tolman-Eichenbaum Machine (Whittington et al., 2020) and related models suggest that some of the representational structures discovered by deep networks — in particular, grid-cell-like codes for spatial and conceptual relationships — may also be fundamental to biological cognition. If this is right, then learning mechanics and theoretical neuroscience may eventually converge on a shared set of principles.

### 6.7 The Foundations of Theoretical Practice: Validity of Approximations

A final set of open problems is methodological. The learning mechanics program depends on a set of approximations — the infinite-width limit, mean-field assumptions, low-rank approximations to weight matrices, power-law models for data geometry — whose regime of validity for realistic systems is often unclear. A mature science must be honest about the boundaries of its models, and this requires a systematic program for testing the validity of approximations as models scale.

For instance, the NTK and μP frameworks are exact in the infinite-width limit, but practical models have large but finite width. How large does a network need to be before the infinite-width predictions become reliable, and how do finite-width corrections scale? For LLMs with billions of parameters, the answer may be "very large" — but this must be empirically established, not assumed. Similarly, scaling laws have been demonstrated over 3–6 orders of magnitude in compute; whether they continue to hold over the next several orders of magnitude is an empirical question of great practical importance.

The development of learning mechanics as a rigorous science requires not just deriving results in tractable limits, but systematically characterizing and testing the corrections to those limits in realistic settings.

---

## 7. Discussion: Physics Before Its Newton

The picture that emerges from this survey is of a science in a historically recognizable transitional state. Robust empirical regularities exist — scaling laws are the deep learning equivalent of Boyle's law for gases, or Snell's law for optics. Powerful theoretical tools are available — the NTK, μP, mean-field theory, SLT, mechanistic interpretability — each of which captures important aspects of the full story. The five pillars of learning mechanics are individually impressive and are beginning to form a coherent picture when viewed together.

What is missing is unification. Just as thermodynamics in 1820 had Boyle's law, Charles' law, Gay-Lussac's law, and Carnot's insight about heat engines, without yet having the statistical mechanics of Boltzmann and Gibbs that would explain all of these from first principles, learning mechanics has scaling laws, the NTK, the edge of stability, and the lazy/rich dichotomy, without yet having the unified framework from which all of these would emerge as special cases. The Newton, or the Boltzmann, of deep learning has not yet appeared.

The analogy to thermodynamics is instructive for one further reason. Thermodynamics did not just explain phenomena that were already known; it enabled the design of better heat engines, and more importantly, it made *predictions* about phenomena that had not yet been observed, and those predictions were confirmed. A mature learning mechanics should similarly not just explain existing observations but predict new ones — predict the performance of architectures before they are trained, predict the conditions for the emergence of new capabilities, predict the effects of novel training interventions.

The practical stakes of this program are high. In safety-critical applications, the difference between a system whose failure modes can be predicted and one that must simply be empirically tested is the difference between engineering and gambling. A mature theory of deep learning would transform machine learning from an empirical discipline into something more resembling an engineering science — not by eliminating experiment, but by making experiments far more informative and prediction far more reliable.

There is also a deeper scientific motivation. The learning systems we are studying are, from one perspective, the most sophisticated information-processing devices ever built. If they are discovering deep principles of learning and representation, then understanding these principles is one of the most important scientific challenges of our era — not just for engineering, but for understanding intelligence itself.

---

## 8. Conclusion

A scientific theory of deep learning is emerging. It is not yet complete, and it is not yet unified, but it is no longer absent. The five pillars reviewed here — solvable models, tractable limits, empirical laws, theories of hyperparameters, and universal phenomena — each provide genuine explanatory purchase, and they are beginning to constrain and inform each other. The learning mechanics research program, as articulated by Simon et al. (2026) and instantiated across dozens of research groups worldwide, is converging on a coarse-grained, statistical, predictive theory of neural network training — a mechanics of learning analogous in structure and method to the mechanics of classical physics.

The most important open problems — emergence and in-context learning, downstream scaling, fine-tuning and alignment, data geometry, loss landscape theory, and the bridge to neuroscience — are hard, but they are no harder than the problems that statistical mechanics, quantum mechanics, and thermodynamics had to solve to reach maturity. The key resources are in place: the mathematical tools, the empirical infrastructure, the conceptual vocabulary, and a growing community of researchers who think of deep learning theory as a scientific discipline.

The field awaits its unifying synthesis. Given the pace of recent progress, there is reason to believe that synthesis is not far away.

---

## References

Ahn, K., Cheng, X., Daneshmand, H., & Sra, S. (2023). *Transformers learn to implement preconditioned gradient descent for in-context learning.* NeurIPS 2023.

Akyürek, E., Schuurmans, D., Andreas, J., Ma, T., & Zhou, D. (2022). *What learning algorithm is in-context learning? Investigations with linear models.* ICLR 2023.

Andreyev, A., & Beneventano, P. (2024). *Edge of stochastic stability: Revisiting the edge of stability for SGD.* arXiv:2412.20553.

Arora, S., Du, S. S., Hu, W., Li, Z., Salakhutdinov, R., & Wang, R. (2019). *On exact computation with an infinitely wide neural net.* NeurIPS 2019.

Bahri, Y., Dyer, E., Kaplan, J., Lee, J., & Sharma, U. (2021). *Explaining neural scaling laws.* arXiv:2102.06701.

Bahri, Y., Kadmon, J., Pennington, J., Schoenholz, S. S., Sohl-Dickstein, J., & Ganguli, S. (2020). *Statistical mechanics of deep learning.* Annual Review of Condensed Matter Physics, 11, 501–528.

Belkin, M., Hsu, D., Ma, S., & Mandal, S. (2019). *Reconciling modern machine-learning practice and the classical bias–variance trade-off.* PNAS, 116(32), 15849–15854.

Bordelon, B., Atanasov, A., & Pehlevan, C. (2024). *A dynamical model of neural scaling laws.* ICML 2024.

Bordelon, B., & Pehlevan, C. (2022). *Self-consistent dynamical field theory of kernel evolution in wide neural networks.* NeurIPS 2022.

Cabannes, V., Arnal, C., Bouchard, G., & Günther, M. (2023). *Broken neural scaling laws.* arXiv:2210.14891.

Chan, S. C. Y., Dasgupta, I., Kim, J., Kumaran, D., Lampinen, A. K., & Hill, F. (2022). *Data distributional properties drive emergent in-context learning in transformers.* NeurIPS 2022.

Christiano, P., Leike, J., Brown, T., Martic, M., Legg, S., & Amodei, D. (2017). *Deep reinforcement learning from human preferences.* NeurIPS 2017.

Cohen, J., Kaur, S., Li, Y., Kolter, J. Z., & Talwalkar, A. (2021). *Gradient descent on neural networks typically occurs at the edge of stability.* ICLR 2021. arXiv:2103.00065.

Damian, A., Nichani, E., Ge, R., & Lee, J. D. (2023). *Self-stabilization: The implicit bias of gradient descent at the edge of stability.* ICLR 2023. arXiv:2209.15594.

Elhage, N., et al. (2021). *A mathematical framework for transformer circuits.* Transformer Circuits Thread.

Elhage, N., et al. (2022). *Toy models of superposition.* Transformer Circuits Thread.

Goldfeld, Z., & Polyanskiy, Y. (2020). *The information bottleneck problem and its applications in machine learning.* IEEE JSAIT, 1(1), 19–38.

Goldt, S., Loureiro, B., Reeves, G., Krzakala, F., Mezard, M., & Zdeborová, L. (2020). *The Gaussian equivalence of generative models for learning with shallow neural networks.* Mathematical and Scientific Machine Learning (MSML) 2021.

Hanin, B., & Nica, M. (2019). *Finite depth and width corrections to the neural tangent kernel.* ICLR 2020. arXiv:1909.05989.

Henighan, T., et al. (2020). *Scaling laws for autoregressive generative modeling.* arXiv:2010.14701.

Hestness, J., Narang, S., Ardalani, N., Diamos, G., Jun, H., Kianinejad, H., ... & Xing, X. (2017). *Deep learning scaling is predictable, empirically.* arXiv:1712.00409.

Hoffmann, J., et al. (2022). *Training compute-optimal large language models.* arXiv:2203.15556 (Chinchilla).

Hopfield, J. J. (1982). *Neural networks and physical systems with emergent collective computational abilities.* PNAS, 79(8), 2554–2558.

Huh, M., Cheung, B., Wang, T., & Isola, P. (2024). *The Platonic representation hypothesis.* ICML 2024. arXiv:2405.07987.

Jacot, A., Gabriel, F., & Hongler, C. (2018). *Neural tangent kernel: Convergence and generalization in neural networks.* NeurIPS 2018. arXiv:1806.07572.

Ji, Z., & Telgarsky, M. (2019). *The implicit bias of gradient descent on nonseparable data.* COLT 2019.

Kaplan, J., McCandlish, S., Henighan, T., Brown, T. B., Chess, B., Child, R., ... & Amodei, D. (2020). *Scaling laws for neural language models.* arXiv:2001.08361.

Liu, Z., Kitouni, O., Nolte, N., Michaud, E. J., Tegmark, M., & Williams, M. (2022). *Towards understanding grokking: An effective theory of representation learning.* NeurIPS 2022.

Martin, C. H., & Mahoney, M. W. (2021). *Predicting trends in the quality of state-of-the-art neural networks without access to training or testing data.* Nature Communications, 12, 4122.

McKenzie, I. R., et al. (2023). *Inverse scaling: When bigger isn't better.* TMLR 2023.

Murfet, D., et al. (2022). *Deep learning is singular, and that's good.* arXiv (openreview).

Nanda, N., Chan, L., Lieberum, T., Smith, J., & Steinhardt, J. (2023). *Progress measures for grokking via mechanistic interpretability.* ICLR 2023. arXiv:2301.05217.

Olah, C., Cammarata, N., Schubert, L., Goh, G., Petrov, M., & Carter, S. (2020). *Zoom in: An introduction to circuits.* Distill.

Olsson, C., et al. (2022). *In-context learning and induction heads.* Transformer Circuits Thread.

Ouyang, L., et al. (2022). *Training language models to follow instructions with human feedback.* NeurIPS 2022.

Poole, B., Lahiri, S., Raghu, M., Sohl-Dickstein, J., & Ganguli, S. (2016). *Exponential expressivity in deep neural networks through transient chaos.* NeurIPS 2016.

Power, A., Burda, Y., Edwards, H., Babuschkin, I., & Misra, V. (2022). *Grokking: Generalization beyond overfitting on small algorithmic datasets.* arXiv:2201.02177.

Roberts, D. A., Yaida, S., & Hanin, B. (2022). *The principles of deep learning theory.* Cambridge University Press.

Saxe, A. M., McClelland, J. L., & Ganguli, S. (2014). *Exact solutions to the nonlinear dynamics of learning in deep linear neural networks.* ICLR 2014.

Schaeffer, R., Miranda, B., & Koyejo, S. (2023). *Are emergent abilities of large language models a mirage?* NeurIPS 2023.

Schoenholz, S. S., Gilmer, J., Ganguli, S., & Sohl-Dickstein, J. (2017). *Deep information propagation.* ICLR 2017.

Simon, J., Kunin, D., Atanasov, A., Boix-Adserà, E., Bordelon, B., Cohen, J., Ghosh, N., Guth, F., Jacot, A., Kamb, M., Karkada, D., Michaud, E. J., Ottlik, B., & Turnbull, J. (2026). *There will be a scientific theory of deep learning.* arXiv:2604.21691.

Singh, A. K., et al. (2023). *The transient nature of emergent in-context learning in transformers.* arXiv:2311.08360.

Song, M., & Yun, C. (2023). *Trajectory alignment: Understanding the edge of stability phenomenon via bifurcation theory.* arXiv:2307.04204.

Soudry, D., Hoffer, E., Nacson, M. S., Gunasekar, S., & Srebro, N. (2018). *The implicit bias of gradient descent on separable data.* JMLR, 19(70).

Tishby, N., Pereira, F., & Bialek, W. (1999). *The information bottleneck method.* Allerton Conference, 1999.

Valiant, L. G. (1984). *A theory of the learnable.* Communications of the ACM, 27(11), 1134–1142.

Vapnik, V. N., & Chervonenkis, A. Ya. (1971). *On the uniform convergence of relative frequencies of events to their probabilities.* Theory of Probability and Its Applications, 16(2), 264–280.

Von Oswald, J., Niklasson, E., Randazzo, E., Sacramento, J., Mordvintsev, A., Zhmoginov, A., & Vladymyrov, M. (2022). *Transformers learn in-context by gradient descent.* ICML 2023.

Wang, K. R., et al. (2022). *Interpretability in the wild: A circuit for indirect object identification in GPT-2 small.* ICLR 2023.

Watanabe, S. (2009). *Algebraic geometry and statistical learning theory.* Cambridge University Press.

Watanabe, S. (2018). *Mathematical theory of Bayesian statistics.* CRC Press.

Wei, J., et al. (2022). *Emergent abilities of large language models.* TMLR 2022.

Xie, S. M., Raghunathan, A., Liang, P., & Ma, T. (2022). *An explanation of in-context learning as implicit Bayesian inference.* ICLR 2022.

Yamins, D. L. K., & DiCarlo, J. J. (2016). *Using goal-driven deep learning models to understand sensory cortex.* Nature Neuroscience, 19(3), 356–365.

Yang, G., & Hu, E. J. (2021). *Feature learning in infinite-width neural networks.* ICML 2021. arXiv:2011.14522.

Yang, G., et al. (2022). *Tensor programs V: Tuning large neural networks via zero-shot hyperparameter transfer.* arXiv:2203.03466.

Zhang, C., Bengio, S., Hardt, M., Recht, B., & Vinyals, O. (2017). *Understanding deep learning requires rethinking generalization.* ICLR 2017.

---

*© 2026. This article is intended for open academic circulation. All referenced works are cited to their primary sources; the reader is encouraged to consult the original papers for full technical details.*
