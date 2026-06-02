# SeClaw

<p align="center">
  <b>SeClaw: Spec-Driven Security Task Synthesis for Evaluating Autonomous Agents</b>
</p>

<p align="center">
  <a href="https://arxiv.org/abs/2606.02302">Paper</a> •
  <a href="https://seclaw-eval.github.io/seclaw-eval/">Project Page</a> •
  <a href="https://github.com/seclaw-eval/seclaw-eval">Code</a> •
  <a href="#citation">Citation</a>
</p>

---

## Overview

**SeClaw** is a framework for evaluating the security risks of autonomous LLM agents operating in stateful environments.

Modern LLM agents are increasingly equipped with tools, files, memory, and access to external services. These capabilities enable complex real-world workflows, but they also introduce new security risks that are difficult to measure with existing benchmarks. Current agent security evaluations often rely on manually designed tasks, cover only a limited set of emerging threats, and mainly focus on final outputs rather than the execution processes that lead to unsafe behavior.

To address these limitations, SeClaw introduces a framework that combines:

* **Specification-driven security task synthesis**, which enables scalable and controllable generation of security evaluation tasks from structured risk specifications;
* **Execution-based security evaluation**, which evaluates agent behavior in a standardized environment;
* **Trajectory-aware assessment**, which analyzes unsafe actions during the agent execution process rather than only checking final responses.

By connecting systematic task synthesis with reproducible security evaluation, SeClaw provides a practical foundation for measuring, diagnosing, and comparing security failures in autonomous LLM agents.

---

## Framework

The overall framework of SeClaw is shown below.

<!-- 
Put the framework figure at:

docs/assets/framework.png

Then the figure will be displayed below.
-->

<p align="center">
  <img src="docs/assets/framework.png" width="85%" alt="SeClaw Framework">
</p>

<!-- 
If the figure is not ready yet, you can temporarily replace the image block above with:

<p align="center">
  <b>[Framework figure will be inserted here]</b>
</p>
-->

---

## Key Features

### Spec-Driven Security Task Synthesis

SeClaw constructs security evaluation tasks from structured risk specifications. This makes the benchmark more scalable, controllable, and extensible compared with manually curated task sets.

### Standardized Execution Environment

SeClaw provides a Docker-based testbed for evaluating autonomous agents under diverse safety-risk scenarios. This allows agent behavior to be tested in a reproducible and controlled environment.

### Broad Security Risk Coverage

The benchmark covers security risks arising from multiple sources, including:

* Agent-accessible resources;
* User tasks and instructions;
* Stateful environments;
* Intrinsic agent behaviors.

### Trajectory-Aware Evaluation

Instead of only evaluating the final answer, SeClaw supports execution-level analysis of agent trajectories. This enables the benchmark to identify unsafe intermediate actions that may not be visible from final outputs alone.

---

## Resources

* **Paper:** https://arxiv.org/abs/2606.02302
* **Project Page:** https://seclaw-eval.github.io/seclaw-eval/
* **Code:** Coming soon
* **Dataset:** Coming soon
* **Evaluation Environment:** Coming soon

---

## Repository Structure

```text
seclaw-eval/
├── README.md
└── docs/
    ├── index.html
    └── assets/
        └── framework.png
```

---

## Citation

If you find this project useful, please cite our paper:

```bibtex
@misc{cheng2026seclawspecdrivensecuritytask, 
      title={SeClaw: Spec-Driven Security Task Synthesis for Evaluating Autonomous Agents},  
      author={Hao Cheng and Changtao Miao and Tianle Song and Yin Wu and He Liu and Erjia Xiao and Junchi Chen and Xiaoyu Shi and Yichi Wang and Jing Yang and Taowen Wang and Jinhao Duan and Mengshu Sun and Peiyan Dong and Xuan Shen and Yang Cao and Renjing Xu and Kaidi Xu and Jindong Gu and Bo Zhang and Jize Zhang and Chenhao Lin and Philip Torr and Chao Shen}, 
      year={2026}, 
      eprint={2606.02302}, 
      archivePrefix={arXiv}, 
      primaryClass={cs.CR}, 
      url={https://arxiv.org/abs/2606.02302},  
}
```

---

## License

The license will be announced soon.
