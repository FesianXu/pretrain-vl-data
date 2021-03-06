# Pretrain-VL-Data
For collecting a large-scale Vision-and-Language (V+L) dataset for pre-training, one naive way could be merging all existing V+L datasets.
However, it is not trivial as we need to preserve the ``fairness" when finetuning and evaluating on the downstream tasks -
we should make sure none of the downstream testing images or sentences are seen during the pre-training.

This code collects the pre-trained V+L Data from [COCO](http://cocodataset.org/#download), [Visual Genome](https://visualgenome.org/api/v0/api_home.html), [Conceptual Captions](https://ai.google.com/research/ConceptualCaptions), and [SBU Captions](http://www.cs.virginia.edu/~vicente/sbucaptions/), as how [UNITER](https://arxiv.org/pdf/1909.11740.pdf) does.

# Note
As specified in the appendix of [UNITER](https://arxiv.org/pdf/1909.11740.pdf), our full pre-trained dataset is composed of four existing V+L datasets: COCO, Visual Genome, Conceptual Captions, and SBU Captions.
The dataset collection is not simply combining them, as we need to make sure none of the downstream evaluation images are seen during pre-training.
Among them, COCO is the most tricky one to clean, as several downstream tasks are built based on it.
The following figure lists the splits from VQA, Image-Text Retrieval, COCO Captioning, RefCOCO/RefCOCO+/RefCOCOg, and the bottom-up top-down (BUTD) detection, all from COCO images.

As observed, the validation and test splits of different tasks are scattered across the raw COCO splits.
Therefore, we exclude all those evaluation images that appeared in the downstream tasks.
In addition, we also exclude all co-occurring Flickr30K images via URL matching, making sure the zero-shot image-text retrieval evaluation on Flickr is fair.
The remaining images become the COCO subset within our full dataset, as shown in the bottom row of this figure.
We apply the same rules to Visual Genome, Conceptual Captions, and SBU Captions.

<p align="center">
  <img src="misc/splits.png" width="70%"/>
</p>

By running this code, we should be able to get the in-domain data as follows:
<p align="center">
  <img src="misc/statistics.png" width="70%"/>
</p>

# Prepare Data
Download [COCO](http://cocodataset.org/#download), [VG](https://visualgenome.org/api/v0/api_home.html), [Refer](https://github.com/lichengunc/refer), [Flickr30K](http://bryanplummer.com/Flickr30kEntities/), [Karpathy's splits](https://github.com/peteanderson80/bottom-up-attention/tree/master/data/genome/coco_splits), and organize the data as the follows:

```
$DATA_PATH
├── coco
│   ├── annotations
│   └── karpathy_splits
├── flickr30k
│   └── flickr30k_entities
├── refer
│   ├── refclef
│   ├── refcoco
│   ├── refcoco+
│   └── refcocog
├── vg
│   ├── image_data.json
│   └── region_descriptions.json
├── sbu_captions
└── conceptual_captions
```

# Run scripts
Run the following scripts:
```bash
# rule out RefCOCO's val+test and Flickr30K images.
python prepro/get_excluded_iids.py

# collect coco's captions
python prepro/collect_coco_captions.py

# collect VG's captions
python prepro/collect_vg_captions.py

# collect SBU's captions
python prepro/collect_sbu_captions.py
```

# Citation

If you find this code useful for your research, please consider citing:
```
@article{chen2019uniter,
  title={Uniter: Learning universal image-text representations},
  author={Chen, Yen-Chun and Li, Linjie and Yu, Licheng and Kholy, Ahmed El and Ahmed, Faisal and Gan, Zhe and Cheng, Yu and Liu, Jingjing},
  journal={arXiv preprint arXiv:1909.11740},
  year={2019}
}
```

## License

MIT
