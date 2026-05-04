<div align="center">

# MMSI-Video-Bench: A Holistic Benchmark for Video-Based Spatial Intelligence

</div>

## Features of MMSI-Video-Bench

MMSI-Video-Bench is a fully human-annotated benchmark for evaluating video-based spatial intelligence in MLLMs. It implements a four-level framework—Perception, Planning, Prediction, and Cross-Video Reasoning, through 1,106 questions grounded in 1,278 video clips sourced from 25 public datasets as well as in-house recordings.

- **High Quality.** All data are manually annotated by 11 domain experts in 3D vision, following a rigorous review and acceptance process to ensure annotation accuracy and reliability.

- **Challenging.** We evaluate 25 strong open-source and proprietary MLLMs, revealing a striking human–AI gap, even the best reasoning model trails human performance by nearly 60%.

- **Diverse Video Sources.** MMSI-Video-Bench includes videos from 25 public datasets and 1 in-house collection, spanning tabletop recordings, indoor and multi-floor environments, outdoor scenes, sports, and movie footage.

- **Comprehensive and Holistic Task Coverage.** The benchmark covers spatial layout reasoning, motion understanding, decision-making, and cross-video reasoning, providing a more holistic evaluation of video-based spatial intelligence.


<div style="text-align: center;">
    <img src="assets/teasor.png" alt="Dialogue_Teaser" width=100% >
</div>





## Example

The questions in MMSI-VIdeo-Bench span 5 major categories: **(1).Spatial Construction**：This category focuses on spatial attributes of instances and scenes, as well as spatial relationships among instances, scenes, and cameras (six subtypes in total); **(2).Motion Understanding**:
This includes understanding camera motion, instance motion, and interactive motion between instances; **(3).Planning** based on spatiotemporal video information;  **(4).Prediction**: Assessing a model’s ability to predict, anticipate, or imagine future states based on the observed video; **(5).Cross-Video Reasoning**: This involves memory update across temporally separated video segments and multi-view integration across videos captured from different viewpoints.


<div style="text-align: center;">
    <img src="assets/benchmark_samples.png" alt="Dialogue_Teaser" width=100% >
</div>

## 📊 Data Details

All of our data is available on https://www.kaggle.com/datasets/abcdwdedwefw/mmsi-video-bench and includes the following components:

🎥 **Video Data** (`videos.zip`): Contains the video clip file (.mp4) corresponding to each sample. This file is generally not required for most models.

🎥 **Frame Data** (`frames.zip`): Contains the frames (.jpg) extracted from each sample's video at the **base sampling rate**. This rate ensures no key information loss during sampling.  Each frame file is named using the format `{timestamp}_frame_{base_interval}_{image_id}` (e.g., 00:06.00_frame_1.50_4), where the timestamp, also shown on the **top-left corner** of the frame, indicates its **capture time in the original recording**.

🖼️ **Reference Image Data** (`ref_images.zip`): Contains the auxiliary images referenced in the questions for each sample.

📝 **Text Annotation** (`mmsivideo.json`)：This file contains the annotation information for MMSI-Video-Bench. All time references in the questions correspond to the capture time in the original recording and **align with** the timestamp flag on each frame. Key fields include:

```
{
  "ref_images": [Paths to auxiliary images referenced in the question,...],
  "video_list": [
    {
      "path": Video clip file path.
      "start": Timestamp (in seconds) of the first frame of the video clip in the original recording.
      "end": Timestamp (in seconds) of the last frame of the video clip in the original recording.
      "base_fps": Base sampling rate.
    },
    ...
  ],
  "frames_list": [[Paths to frames sampled at the base sampling rate,...],...]
  "system_prompt": "...".
  "task_prompt": Task-specific prompt.
  "user_prompt": Question text, with <video> as a placeholder for video and <image> for auxiliary images.
  "format_prompt": Output format requirements.
  "ground_truth": Correct answer.
  "hint": The rationale used when reviewing annotation questions for acceptance, which can serve as a reference prompt if the problem is too difficult.
}
```


Unless otherwise specified, the model input generally consists of:
`system_prompt + task_prompt + user_prompt + format_prompt`.


## 🚀 Quick Start

As outlined in our paper, we support two evaluation settings: **Sufficient-Coverage** ensures lossless sampling by preserving all essential information, while **Uniform‑50** uniformly samples 50 frames and cannot guarantee full information retention. Although most models—due to API constraints or GPU memory limits—only support the Uniform‑50 setting, we still strongly recommend evaluating under the **Sufficient‑Coverage** setting whenever possible.


1. Data Preparation: After downloading the data, unzip the files and organize the directory as follows:
 
    ```
    data/
    ├── mmsivideo.json
    ├── frames/
    ├── ref_images/
    ├── videos/
    ```
2. Install Required Packages. If you plan to evaluate open-source models, please set up their corresponding environments.
    ```
    pip install -r requirements.txt
    ```
3. Run Inference & Evaluation: For proprietary models: Update the `base_url` and `api_key` in `utils/openai_api.py` with your own credentials. For open-source models: Modify the `load_model` function in `inference.py` to point to the corresponding model path. Run the following commands to perform inference for a specific model under a given setting, and evaluate the results on a specific benchmark (Main, Robot Bench, Indoor Scene Perception Bench or Grounding Bench):
    ```python
    python inference.py --model_name {model_name} --setting Uniform-50/Sufficient-Coverage # inference
    python evaluation.py --eval_dir {path/to/results} --bench main/robot_bench/ground_bench/indoor_perception_bench/easy2hard_bench # evaluation
    ```
  
## 🏆 Leaderboard

<details> <summary>📦 Uniform-50 Setting</summary>

| Model                      | Avg.(%) | Type        |
|----------------------------|---------|-------------|
| Human                   | 96.40   | Baseline    |
|🥇Gemini 3 pro            | 37.97   | Proprietary |
|🥈 O3                      | 36.98   | Proprietary |
|🥉GPT-5                      | 36.80   | Proprietary |
| Gemini 2.5 Flash           | 35.44   | Proprietary |
| Gemini 2.5 Flash (Thinking) | 35.17   | Proprietary |
| Seed-1.6-vision            | 34.87   | Proprietary |
| Claude-haiku-4.5           | 34.27   | Proprietary |
| O4-mini                    | 34.18   | Proprietary |
| QwenVL2.5-72B              | 32.73   | Open-Source |
| InternVL3-78B              | 32.55   | Open-Source |
| Doubao-1.5-thinking        | 31.65   | Proprietary |
| GPT-4o                     | 31.56   | Proprietary |
| InternVL2.5-78B            | 31.37   | Open-Source |
| InternVL2.5-38B            | 31.01   | Open-Source |
| QwenVL3-30B (Thinking)      | 30.83   | Open-Source |
| LLaVA-Video-72B            | 30.38   | Open-Source |
| InternVL3-8B               | 30.38   | Open-Source |
| QwenVL2.5-VL-7B-Instruct   | 29.66   | Open-Source |
| InternVL2.5-8B             | 29.11   | Open-Source |
| InternVL3-38B              | 28.84   | Open-Source |
| QwenVL3-30B                | 28.75   | Open-Source |
| QwenVL2.5-32B              | 28.57   | Open-Source |
| LLaVA-Video-7B             | 28.48   | Open-Source |
| QwenVL3-8B                 | 27.58   | Open-Source |
| InternVideo2.5-8B          | 27.40   | Open-Source |
| Random Guessing            | 24.10   | Baseline    |

</details>

<details> <summary>📦 Sufficient-Coverage Setting</summary>

| Model                      | Avg.(%) | Type        |
|----------------------------|---------|-------------|
| Human                      | 96.4    | Baseline    |
| 🥇O3                         | 37.34   | Proprietary |
| 🥈Gemini 2.5 Flash (Thinking) | 36.71   | Proprietary |
| 🥉Gemini 2.5 Flash           | 36.62   | Proprietary |
| O4-mini                    | 35.08   | Proprietary |
| QwenVL2.5-32B              | 32.37   | Open-Source |
| QwenVL2.5-72B              | 31.83   | Open-Source |
| InternVL3-8B               | 29.57   | Open-Source |
| QwenVL3-30B                | 29.11   | Open-Source |
| QwenVL3-8B                 | 29.09   | Open-Source |
| QwenVL2.5-7B               | 28.84   | Open-Source |
| InternVL2.5-8B             | 28.66   | Open-Source |
| GPT-4o                     | 28.12   | Proprietary |
| QwenVL3-30B (Thinking)      | 28.03   | Open-Source |
| InternVideo2.5-8B          | 26.85   | Open-Source |
| Random Guessing            | 24.10   | Baseline    |

</details>

<details> <summary>🤖 Robot Sub-Bench</summary>

| Model                      | Avg.(%) | Type        |
|----------------------------|---------|-------------|
| 🥇Gemini 3 Pro               | 40.20   | Proprietary |
| 🥈Gemini 2.5 Flash (Thinking) | 39.71   | Proprietary |
| 🥉Seed-1.6-vision            | 39.34   | Proprietary |
| O3                         | 39.22   | Proprietary |
| QwenVL2.5-72B              | 37.75   | Open-Source |
| InternVL3-8B               | 37.75   | Open-Source |
| GPT-5                      | 37.75   | Proprietary |
| InternVL2.5-38B            | 36.27   | Open-Source |
| Doubao-1.5-thinking        | 36.07   | Proprietary |
| Gemini 2.5 Flash           | 35.78   | Proprietary |
| O4-mini                    | 35.29   | Proprietary |
| QwenVL2.5-7B               | 34.8    | Open-Source |
| InternVL2.5-78B            | 34.8    | Open-Source |
| Claude-haiku-4.5           | 34.8    | Proprietary |
| InternVL3-78B              | 34.31   | Open-Source |
| LLaVA-Video-72B            | 34.31   | Open-Source |
| QwenVL3-30B                | 32.84   | Open-Source |
| QwenVL2.5-32B              | 32.84   | Open-Source |
| QwenVL3-8B                 | 32.12   | Open-Source |
| InternVideo2.5-8B          | 29.90   | Open-Source |
| GPT-4o                     | 29.90   | Proprietary |
| InternVL2.5-8B             | 28.43   | Open-Source |
| InternVL3-38B              | 27.94   | Open-Source |
| QwenVL3-30B (Thinking)      | 27.94   | Open-Source |
| LLaVA-Video-7B             | 24.51   | Open-Source |


</details>

<details> <summary>🏠 Indoor Scene Perception Sub-Bench</summary>

| Model                      | Avg.(%) | Type        |
|----------------------------|---------|-------------|
| 🥇GPT-5                      | 41.68   | Proprietary |
| 🥈O3                         | 40.73   | Proprietary |
| 🥉Gemini 2.5 Flash           | 39.39   | Proprietary |
| Gemini 3 Pro               | 39.39   | Proprietary |
| Gemini 2.5 Flash (Thinking) | 37.86   | Proprietary |
| O4-mini                    | 37.48   | Proprietary |
| Seed-1.6-vision            | 34.2    | Proprietary |
| Claude-haiku-4.5           | 33.46   | Proprietary |
| Doubao-1.5-thinking        | 33.04   | Proprietary |
| InternVL3-78B              | 32.5    | Open-Source |
| QwenVL3-30B (Thinking)      | 32.31   | Open-Source |
| GPT-4o                     | 31.74   | Proprietary |
| QwenVL2.5-72B              | 30.78   | Open-Source |
| InternVL2.5-78B            | 30.4    | Open-Source |
| QwenVL3-30B                | 30.02   | Open-Source |
| QwenVL2.5-32B              | 29.64   | Open-Source |
| InternVL2.5-8B             | 29.45   | Open-Source |
| InternVL3-38B              | 29.06   | Open-Source |
| QwenVL3-8B                 | 28.68   | Open-Source |
| InternVL2.5-38B            | 28.3    | Open-Source |
| LLaVA-Video-72B            | 28.11   | Open-Source |
| InternVL3-8B               | 27.72   | Open-Source |
| LLaVA-Video-7B             | 27.53   | Open-Source |
| QwenVL2.5-7B               | 27.15   | Open-Source |
| InternVideo2.5-8B          | 26.77   | Open-Source |


</details>

<details> <summary>📍 Grounding Sub-Bench</summary>

| Model                      | Avg.(%) | Type        |
|----------------------------|---------|-------------|
| 🥇Gemini 2.5 Flash           | 38.81   | Proprietary |
| 🥈Gemini 2.5 Flash (Thinking) | 38.21   | Proprietary |
| 🥉O3                         | 37.61   | Proprietary |
| Doubao-1.5-thinking        | 37.05   | Proprietary |
| InternVL3-78B              | 35.52   | Open-Source |
| GPT-5                      | 35.22   | Proprietary |
| Gemini 3 Pro               | 35.22   | Proprietary |
| O4-mini                    | 34.33   | Proprietary |
| QwenVL2.5-72B              | 34.33   | Open-Source |
| Seed-1.6-vision            | 33.04   | Proprietary |
| Claude-haiku-4.5           | 32.84   | Proprietary |
| InternVL2.5-38B            | 31.94   | Open-Source |
| InternVL3-8B               | 31.94   | Open-Source |
| GPT-4o                     | 31.94   | Proprietary |
| QwenVL3-30B (Thinking)      | 31.64   | Open-Source |
| QwenVL2.5-32B              | 31.04   | Open-Source |
| LLaVA-Video-72B            | 31.04   | Open-Source |
| InternVL3-38B              | 30.45   | Open-Source |
| InternVL2.5-8B             | 30.15   | Open-Source |
| InternVL2.5-78B            | 29.85   | Open-Source |
| QwenVL3-30B                | 29.25   | Open-Source |
| QwenVL2.5-7B               | 28.66   | Open-Source |
| QwenVL3-8B                 | 28.66   | Open-Source |
| InternVideo2.5-8B          | 27.76   | Open-Source |
| LLaVA-Video-7B             | 27.16   | Open-Source |

</details>

*Note: For the three sub-benchmarks, we take the higher score of each model across the two settings for easier presentation.*
