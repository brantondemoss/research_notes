% [Learning by Cheating](http://proceedings.mlr.press/v100/chen20a/chen20a.pdf)

<center> Dian Chen, Brady Zhou, Vladlen Koltun, Philipp Krahenbuhl

![](cheating.png)

> Figure 1: Overview of our approach. (a) An agent with access to privileged information learns to imitate expert demonstrations. This agent learns a robust policy by cheating. It does not need to learn to see because it gets direct access to the environment’s state. (b) A sensorimotor agent without access to privileged information then learns to imitate the privileged agent. The privileged agent is a “white box” and can provide high-capacity on-policy supervision. The resulting sensorimotor agent does not cheat.

This paper improves upon the always-brilliant DAgger [^1] framework, by first training a Behaviour Cloning agent with *priveledged information*, then running online simulated DAgger and using the BC agent to supervise the corrective actions.

By priveledged information, they mean something like the *full state*, rather than something filtered through an observation function (e.g. only camera images, what you can see at the current moment, etc...) In particular, they represent the local scene in a top-down bird's eye view, and feed that in to the imitation learner. They can do this because they're working in simulation and have access to the full state.

After training to convergence, the priveledged BC agent now supervises the training of another imitation learning agent which only receives observations (RGB images). The priveledged agent continues to receive the full state, and now the RGB agent is allowed to unroll in the simulation on-policy, and receives supervisory signals to match the full policy of the teacher. Apparently the student network can almost completely recover the teacher policy, and now onlyu needs RGB observations to achieve equal performance!

The authors argue that this works because the teacher network only needs to learn how to act, not to perceive, and the student network then only needs to learn how to perceive, because of the strong action supervision by the teacher. I wonder if this is really the case, but it's amazing that a trick as simple as this works so well. I wonder what extra information the full policy (not just the executed expert action) contains - didn't Hinton or someone say that the logits of policies used for distillation contain "dark knowledge"? E.g. you can imagining softening an ensemble of classifiers on some labels, and training on that rather than the true class label, and the logit distribution tells you much more about the properties of the data. "Softened outputs reveal the dark knowledge in the ensemble." [^2]

The work is still limited by needing on-policy rollouts for the distillation step. This would still be infeasible to run in the real world, so they run it in a simulator. What do you do when you don't have access to a simulator though...?

I wonder if companies like Tesla are trying tricks like this - they clearly have the BEV working, and enough offline compute to calculate all the state information they could want... Rather than being priveledged about the current state, I wonder if being priveledged with respect to the future can help with learning good test-time policies... This is a bit like what the VQM paper does with envoding future variations with a future-privledged latent code.

[^1]: [A Reduction of Imitation Learning and Structured Prediction to No-Regret Online Learning](https://www.cs.cmu.edu/~sross1/publications/Ross-AIStats11-NoRegret.pdf)

[^2]: [Dark knowledge: Hinton et al.](https://www.ttic.edu/dl/dark14.pdf)
