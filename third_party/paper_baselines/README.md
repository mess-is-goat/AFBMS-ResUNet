# Pinned model source for offline AutoDL training

These are unchanged source files exported from the authors' Git blobs, with their license texts preserved beside them. No pretrained weights are included.

| Local model file | Author repository | Commit | SHA-256 of model file | License |
| --- | --- | --- | --- | --- |
| `doubleblock/DB_MaxViT.py` | [Laptq201/DoubleBlock-ViT-Unet-segment](https://github.com/Laptq201/DoubleBlock-ViT-Unet-segment) | `5b617d72608986f38e602f4dd69b95d45443f5c7` | `1b9caf37be4b0c1b4fd00d16dd9ef4dfd71610bcf2e009c1bc4124aa5176c8b2` | MIT; see `doubleblock/LICENSE` |

`models/paper_baselines.py` checks these SHA-256 values before import. DoubleBlock's top-level CUDA demo statements are skipped at load time without modifying this file.
