# TA2V: Text-Audio Guided Video Generation
This is the reimplementation and extension of Text&Audio-guided Video Maker (TAgVM) model for TA2V task base on (https://github.com/Minglu58/TA2V). We pay more attention to model inference and performance evaluation.

<img width="1150" alt="Model Inference workflow" src="https://github.com/uwanny/11785Project/blob/main/figure/project.png">

## Examples
### Music Performance Videos

![generation_stage2_5_db_39_Jerusalem_96_21](https://github.com/uwanny/11785Project/blob/main/result_URMP/results/generation_stage2_5_db_39_Jerusalem_96_21.gif)
![generation_stage2_3_va_44_K515_136_16](https://github.com/uwanny/11785Project/blob/main/result_URMP/results/generation_stage2_3_va_44_K515_136_16.gif)


### Landscape Videos
![generation_stage2_fire_crackling_136_1_44](https://github.com/uwanny/11785Project/blob/main/results_landscape/generation_stage2_fire_crackling_136_1_44.gif)
![generation_stage2_squishing_water_134_6_45](https://github.com/uwanny/11785Project/blob/main/results_landscape/generation_stage2_squishing_water_134_6_45.gif)


## Sampling Procedure
### Sample Short Music Performance Videos
- `gpt_text_ckpt`: path to GPT checkpoint
- `vqgan_ckpt`: path to video VQGAN checkpoint
- `data_path`: path to dataset, you can change it to `post_landscape` for Landscape-VAT dataset
- `load_vid_len`: for URMP-VAT, it is set to `90` (fps=30); for Landscape-VAT, it is set to `30` (fps=10)
- `text_emb_model`: model to encode text, choices: `bert`, `clip`
- `audio_emb_model`: model to encode audio, choices: `audioclip`, `wav2clip`
- `text_stft_cond`: load text-audio-video data
- `n_sample`: the number of videos need to be sampled
- `run`: index for each run
- `resolution`: resolution used in training video VQGAN procedure
- `model_output_size`: the resolution when training the diffusion model
- `audio_guidance_lambda`: coefficient to control audio guidance
- `direction_lambda`: coefficient to control semantic change consistency of audio and video
- `text_guidance_lambda`: coefficient to control text guidance
- `diffusion_ckpt`: path to diffusion model
```
python scripts/sample_tav.py --gpt_text_ckpt /home/ubuntu/saved_ckpts/landscape-VAT_GPT.ckpt \
--vqgan_ckpt /home/ubuntu/saved_ckpts/landscape-VAT_video_VQGAN.ckpt --text_emb_model bert \
--data_path /home/ubuntu/11785Project/datasets/post_landscape/ --top_k 2048 --top_p 0.80 --n_sample 50 --run 17 --dataset landscape --audio_emb_model audioclip --resolution 96 --batch_size 1 --model_output_size 128 --noise_schedule cosine \
--iterations_num 1 --audio_guidance_lambda 10000 --direction_lambda 5000 --text_guidance_lambda 10000 \
--diffusion_ckpt /home/ubuntu/saved_ckpts/landscape-VAT_diffusion.pt
```
```
python scripts/sample_tav.py --gpt_text_ckpt /home/ubuntu/saved_ckpts/URMP-VAT_GPT.ckpt --text_stft_cond \
--vqgan_ckpt /home/ubuntu/saved_ckpts/URMP-VAT_video_VQGAN.ckpt --text_emb_model bert \
--data_path /home/ubuntu/11785Project/datasets/post_URMP/ --top_k 2048 --top_p 0.80 --n_sample 50 --run 17 --dataset URMP --audio_emb_model audioclip --resolution 96 --batch_size 1 --model_output_size 128 --noise_schedule cosine \
--iterations_num 1 --audio_guidance_lambda 10000 --direction_lambda 5000 --text_guidance_lambda 10000 \
--diffusion_ckpt /home/ubuntu/saved_ckpts/URMP-VAT_diffusion.pt
```
### Calculate Evaluation Metrics
- `exp_tag`: name of result folder, which is under `results` folder
- `audio_folder`: audio folder name, default: `audio`
- `fake2_video_folder`: video folder name fake stage2, default: `fake_stage2`
- `txt_folder`: text folder name, default: `txt`
* **CLIP audio score**
```
python tools/clip_score/clip_audio.py --exp_tag 17_tav_landscape --audio_folder audio --fake2_video_folder fake_stage2 --audio_emb_model audioclip
```
* **CLIP text score**
```
python tools/clip_score/clip_text.py --exp_tag 17_tav_landscape --txt_folder txt --fake2_video_folder fake_stage2
```

- `real_folder`: ground-truth video folder name, default: `real`
- `fake2_folder`: generated stage 2 video folder name, default: `fake_stage2`
- `fake1_folder`: generated stage 1 video folder name, default: `fake_stage1`
- `mode`: mode to calculate FVD, FID scores, choices: `full`, `size`
* **FVD**
```
python tools/tf_fvd/fvd.py --exp_tag 17_tav_landscape --real_folder real --fake2_folder fake_stage2 --fake1_folder fake_stage1 --mode full
```
* **FID**
```
python tools/tf_fvd/fid.py --exp_tag 17_tav_landscape --real_folder real --fake2_folder fake_stage2 --fake1_folder fake_stage1 --mode full
```