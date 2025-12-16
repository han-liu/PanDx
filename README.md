# PanDx: AI-assisted Pancreatic Ductal Adenocarcinoma Detection 
[![arXiv](https://img.shields.io/badge/preprint-2503.10068-blue)](https://arxiv.org/abs/2503.10068) [![cite](https://img.shields.io/badge/cite-BibTex-red)](xx) [![leaderboard](https://img.shields.io/badge/Leaderboard-yellow)](https://panorama.grand-challenge.org/evaluation/testing-phase/leaderboard/) [![website](https://img.shields.io/badge/Challenge%20website-50d13d)](https://panorama.grand-challenge.org/)

### This is Team DTI's :trophy: 1st place solution in the PANORAMA Challenge. 

Paper: [AI-assisted Early Detection of Pancreatic Ductal Adenocarcinoma on Contrast-enhanced CT](https://arxiv.org/abs/2503.10068)

<p align="center"><img src="https://github.com/han-liu/PDAC_Detection/blob/main/assets/gt_vs_pred.png" alt="gt_vs_pred" width="750"/></p>

If you find our code/paper helpful for your research, please kindly consider citing our work:
```
@article{liu2025ai,
  title={AI-assisted Early Detection of Pancreatic Ductal Adenocarcinoma on Contrast-enhanced CT},
  author={Liu, Han and Gao, Riqiang and Krieg, Eileen and Grbic, Sasa},
  journal={arXiv preprint arXiv:2503.10068},
  year={2025}
}
```

If you have any questions, feel free to contact han.liu@siemens-healthineers.com or open an Issue in this repo. 

---

### Installation
#### Requirements
```
cuda-11.1, cudnn/9.0.0-cuda-12
```
#### Create a virtual environment:
```
conda create pdac python=3.12 -y
conda activate pdac
```

#### Install dependencies
```
git clone https://github.com/han-liu/PDAC_Detection.git
cd PDAC_Detection
pip install -r requirements.txt

cd packages/nnunetv2
pip install -e .
    
cd ../report-guided-annotation
pip install -e .
```

#### Download the our models and example testing images [[click to download]](https://drive.google.com/drive/folders/1RpbofQDrQNzwfYjFhQYRRWCN8HhIoZQP?usp=sharing)
```
PDAC_Detection/
└── workspace/
    ├── nnUNet_raw/
    ├── nnUNet_preprocessed/
    └── nnUNet_results/
        ├── Dataset103_PANORAMA_baseline_Pancreas_Segmentation/
        └── Dataset107_PDAC_Detection/
    └── test_example/
            ├── output/
            └── input/
                ├── filename1.nii.gz
                ├── filename2.mha
                └── ...
```

### Inference
#### Set up environment variables for nnU-Net
```
export nnUNet_raw="./workspace/nnUNet_raw"
export nnUNet_preprocessed="./workspace/nnUNet_preprocessed"
export nnUNet_results="./workspace/nnUNet_results"
```

#### To test our model, run:
```
python main.py -i ${INPUT_DIR} -o ${OUTPUT_DIR} --inv_alpha ${INV_ALPHA}
```
where:
- `${INPUT_DIR}`  is the directory containing your input images (e.g., nii.gz, mhd, mha, etc).
- `${OUTPUT_DIR}` is the directory where the prediction will be saved.
- `${INV_ALPHA}`  controls the expansion of the predicted lesion (larger values predict larger lesions); default=`15`.

#### For a quick test using the example testing images, run:
```
python main.py -i ./workspace/test_example/input -o ./workspace/test_example/output
```

#### What are the outputs?
- PDAC detection map (ranging from 0-1) where each predicted lesion is assigned a confidence score.
- Patient-level likelihood score (computed as the **maximum** value of the detection map)

The PDAC detection maps are saved under `${OUTPUT_DIR}/pdac-detection-map`:
```
├── ${OUTPUT_DIR}/
    ├── pdac-likelihood.json
    └── pdac-detection-map/
        ├── filename1.nii.gz
        ├── filename2.nii.gz
        └── ...
```

The `pdac-likelihood.json` contains the likelihood scores for each patient:
```
{
    "filename1": 0.9965946078300476,
    "filename2": 0.9977765679359436,
    ...
}
```

### Acknowledgement
This code is built upon the following works. We gratefully acknowledge their contribution and encourage users to cite their original work:
1. Isensee, Fabian, et al. "nnU-Net: a self-configuring method for deep learning-based biomedical image segmentation." Nature methods
2. Bosma, Joeran S, et al. "Semi-supervised learning with report-guided pseudo labels for deep learning–based prostate cancer detection using biparametric MRI." Radiology AI
3. Alves, Natália,  et al. "Fully automatic deep learning framework for pancreatic ductal adenocarcinoma detection on computed tomography." Cancers


