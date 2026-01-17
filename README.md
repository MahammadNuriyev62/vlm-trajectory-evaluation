# VLM Trajectory Evaluation (Small Models)

This repo is a lightweight sanity check for how small VLMs handle trajectory-style
queries (e.g., "follow the SUV", "avoid pedestrians") when given a few candidate
paths as images. The current notebooks use Qwen3-VL 2B model.

NOTE: This is not a rigorous evaluation and the results are not meant to be conclusive. The goal is to explore how small embedding-based VLMs can be used for trajectory evaluation once the candidate trajectories are avaiable by other means (e.g. https://github.com/valeoai/DrivoR).

## Notebooks

`vl-reranker.ipynb`: Uses [Qwen3-VL 2B Reranker](https://huggingface.co/Qwen/Qwen3-VL-Reranker-2B) model to score candidate paths based on a trajectory query. It demonstrates how the model can rank paths according to the given instruction.

In the example below, the model has 3 trajectories it can take, however, based on the instruction it should prefer the path that **follows the black SUV**.

<!-- table with 3 images -->
| Path 1 | Path 2 | Path 3 |
| --- | --- | --- |
| ![Path 1](candidates/follow_car/0dec29a68abd5e4e_1.jpg) | ![Path 2](candidates/follow_car/0dec29a68abd5e4e_2.jpg) | ![Path 3](candidates/follow_car/0dec29a68abd5e4e_3.jpg) |
| Score: 0.6786 | Score: 0.6340 | **__Score: 0.6979__** |

The model correctly identifies the preferred path and scores the Path 3 the highest.

Now, if we instruct the model to turn to the harris, should prefer Path 1, which it exactly does by providing scores as follows:

| Path 1 | Path 2 | Path 3 |
| --- | --- | --- |
| **__Score: 0.7704__** | Score: 0.7551 | Score: 0.7482 |

However, when we asked to follow the **white** SUV, the model fails to identify the correct path and chooses Path 3. Interestingly, when the word "SUV" is changed to "car", the model correctly identifies Path 2 as the best path.

Clearly, this 2B model is small hence sensitive to the exact wording of the instruction and requires careful prompting to get the desired behavior. This highlights the importance of prompt engineering when working with smaller VLMs for trajectory evaluation tasks.

More examples can be found in the [notebook itself](./vl-reranker.ipynb).

The format was inspired by this [paper](https://arxiv.org/abs/2211.13227) which used similar trajectories drawn directly on the image except that all candidates were rendered on a single image and fed to the VLM model along with the instruction to choose safest path.


## Bonus

Model was prompted to avoid pedestrians and it would correctly pick pick Path 3 if not confusing Path 4.
| Path 4 | Path 3 | Path 2 | Path 1 |
| --- | --- | --- | --- |
| ![Path 4](candidates/pedestrians/00aec7bb44f0552a_4.jpg) | ![Path 3](candidates/pedestrians/00aec7bb44f0552a_3.jpg) | ![Path 2](candidates/pedestrians/00aec7bb44f0552a_2.jpg) | ![Path 1](candidates/pedestrians/00aec7bb44f0552a_1.jpg) |
| **__Score: 0.6163__** | Score: 0.5920 | Score: 0.5747 | Score: 0.5571 |

It feels like model sees pedestrians and totally forgets that it should AVOID them.