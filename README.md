# HICO Benchmark

Code for reproducing the results in the following paper:

**HICO: A Benchmark for Recognizing Human-Object Interactions in Images**  
Y.-W. Chao, Z. Wang, Y. He, J. Wang, and J. Deng  
Proceedings of the IEEE International Conference on Computer Vision (ICCV), 2015 

Check out the [project site](http://www.umich.edu/~ywchao/hico/) for more details.

### Citing HICO Benchmark

Please cite HICO Benchmark in your publications if it helps your research:

    @INPROCEEDINGS{chao:iccv2015,
      author = {Yu-Wei Chao and Zhan Wang and Yugeng He and Jiaxuan Wang and Jia Deng},
      title = {HICO: A Benchmark for Recognizing Human-Object Interactions in Images},
      booktitle = {Proceedings of the IEEE International Conference on Computer Vision},
      year = {2015},
    }

### Setup

0. Download and extract the HICO dataset.
  <pre>
  wget http://napoli18.eecs.umich.edu/public_html/data/hico_20150920.tar.gz
  tar -zxvf hico_20150920.tar.gz
  </pre>

0. Clone this repo and change the directory.
  <pre>
  git clone https://github.com/ywchao/hico_benchmark.git
  cd hico_benchmark
  </pre>

0. Create symlinks for the downloaded HICO dataset. `$HICO_ROOT` should contain `images`, `anno.mat`, and `README`.
  <pre>
  ln -sf $HICO_ROOT ./external/hico_20150920
  </pre>

### Download pre-computed DNN features

Our pre-computed DNN features guarantee exact reproduction of the paper's results.

  ```
  ./data/scripts/fetch_dnn_features.sh
  ```

This will populate the `data` folder with `precomputed_dnn_features`.

### Training and evaluating HOI classifiers
0. Start MATLAB `matlab` under `hico_benchmark`. You should see the message `added paths for the experiment!` followed by the MATLAB prompt `>>`.

0. Run `setup` to prepare the required files.
  - Generate the annotation file `anno_iccv.mat` from `anno.mat`. For the default evaluation setting, we add extra ground-truth negatives by sampling "unknown" images for each HOI class (see the paper for details). Using the provided sample set is necessary for exact reproduction of the paper's result.
  - Download and build `liblinear`.

0. The training code will use `parfor`. Uncomment and set `poolsize` in `config.m` according to your need, or leave it commented out if you want MATLAB to set it automatically.

0. Run `train_svm_vo` to start the training of HOI classifiers.
  - By default, the code will run training with DNN features from Alex's Net pretrained on ImageNet, i.e. the "DNN (ImageNet)" in the paper.
  - The code also supports training with the Fine-tune V, Fine-tune O, and Fine-tune VO features, i.e. the "DNN (fine-tune V)", "DNN (fine-tune O)", and "DNN (fine-tune VO)" in the paper. Simply comment out or uncomment each line below in `train_svm_vo.m` to select a feature type.

  <pre>
  feat_type = 'imagenet';
  % feat_type = 'ft_verb';
  % feat_type = 'ft_object';
  % feat_type = 'ft_action';
  </pre>

0. Run `eval_default_run` or `eval_ko_run` to evaluate the trained HOI classifiers in the default or "Known Object (KO)" setting.
  - The code will output mean average precision (mAP) on 600 HOI classes.
  - Similar to `train_svm_vo.m`, you can select the feature type by editing `eval_default_run.m` and `eval_ko_run.m`.

