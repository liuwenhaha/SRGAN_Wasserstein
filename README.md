## SRGAN_Wasserstein
Applying Waseerstein GAN to SRGAN, a GAN based super resolution algorithm.

***This repo was forked from @zsdonghao 's [tensorlayer/srgan](https://github.com/tensorlayer/srgan) repo, based on this original repo, I changed some code to apply wasserstein loss, making the training procedure more stable.***

### SRGAN Architecture
![](http://ormr426d5.bkt.clouddn.com/18-5-18/43943225.jpg)

TensorFlow Implementation of ["Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network"](https://arxiv.org/abs/1609.04802)

### Wasserstein GAN

When the SRGAN was first proposed in 2016, we haven't had [Wasserstein GAN](https://arxiv.org/abs/1701.07875)(2017) yet, WGAN using wasserstein distance to measure the disturibution difference between two data set. As for the original GAN training, we don't know when to stop training the discriminator or the generator, to get a nice result. But when using the wasserstein loss, as the loss decreasing, the result will be better. So we are going to use the WGAN and we are not going to explain the math detail of WGAN here, but to give the following steps to apply WGAN.

* Remove the sigmoid activation from the last layer of the discriminator. (```model.py```, line 218-219)
* Don't take logarithm to the loss of discriminator and generator. (```main.py```, line 105-108)
* Clipping the weights to some contant range [-c, c]. (```main.py```, line 136)
* Don't use the optimizer like adam or momoentum which based on momentum, instead, RMSprop or SGD would be better. (```main.py```, line 132-133)

These above steps was given by an excellent article[[4]](https://zhuanlan.zhihu.com/p/25071913), the arthor explained the WGAN in a very straightforward way, it was written in Chinese.

### Loss curve and Result
![](http://ormr426d5.bkt.clouddn.com/18-5-18/8141442.jpg)

![](http://ormr426d5.bkt.clouddn.com/18-5-18/22508558.jpg)

![](http://ormr426d5.bkt.clouddn.com/18-5-18/83166966.jpg)

![](http://ormr426d5.bkt.clouddn.com/18-5-18/96883821.jpg)


### Prepare Data and Pre-trained VGG

- 1. You need to download the pretrained VGG19 model in [here](https://mega.nz/#!xZ8glS6J!MAnE91ND_WyfZ_8mvkuSa2YcA7q-1ehfSm-Q1fxOvvs) as [tutorial_vgg19.py](https://github.com/zsdonghao/tensorlayer/blob/master/example/tutorial_vgg19.py) show.
- 2. You need to have the high resolution images for training.
  -  In this experiment, I used images from [DIV2K - bicubic downscaling x4 competition](http://www.vision.ee.ethz.ch/ntire17/), so the hyper-paremeters in `config.py` (like number of epochs) are seleted basic on that dataset, if you change a larger dataset you can reduce the number of epochs. 
  -  If you dont want to use DIV2K dataset, you can also use [Yahoo MirFlickr25k](http://press.liacs.nl/mirflickr/mirdownload.html), just simply download it using `train_hr_imgs = tl.files.load_flickr25k_dataset(tag=None)` in `main.py`. 
  -  If you want to use your own images, you can set the path to your image folder via `config.TRAIN.hr_img_path` in `config.py`.

### Run

We run this script under [TensorFlow](https://www.tensorflow.org) 1.4 and the [TensorLayer](https://github.com/tensorlayer/tensorlayer) 1.8.0+.

- Set your image folder in `config.py`, if you download [DIV2K - bicubic downscaling x4 competition](http://www.vision.ee.ethz.ch/ntire17/) dataset, you don't need to change it. 
- Other links for DIV2K, in case you can't find it : [test\_LR\_bicubic_X4](https://data.vision.ee.ethz.ch/cvl/DIV2K/validation_release/DIV2K_test_LR_bicubic_X4.zip), [train_HR](https://data.vision.ee.ethz.ch/cvl/DIV2K/DIV2K_train_HR.zip), [train\_LR\_bicubic_X4](https://data.vision.ee.ethz.ch/cvl/DIV2K/DIV2K_train_LR_bicubic_X4.zip), [valid_HR](https://data.vision.ee.ethz.ch/cvl/DIV2K/validation_release/DIV2K_valid_HR.zip), [valid\_LR\_bicubic_X4](https://data.vision.ee.ethz.ch/cvl/DIV2K/DIV2K_valid_LR_bicubic_X4.zip).

```python
config.TRAIN.img_path = "your_image_folder/"
```

- Start training.

```bash
python main.py
```

- Start evaluation. ([pretrained model](https://github.com/tensorlayer/srgan/releases/tag/1.2.0) for DIV2K)

```bash
python main.py --mode=evaluate 
```

### Reference
* [1] [Photo-Realistic Single Image Super-Resolution Using a Generative Adversarial Network](https://arxiv.org/abs/1609.04802)
* [2] [Is the deconvolution layer the same as a convolutional layer ?](https://arxiv.org/abs/1609.07009)
* [3] [Wasserstein GAN](https://arxiv.org/abs/1701.07875)
* [4] [令人拍案叫绝的Wasserstein GAN](https://zhuanlan.zhihu.com/p/25071913)
* [5] [Professor ho：-知乎文章]() [Chinese verson readme]

### Author
- [zsdonghao](https://github.com/zsdonghao)
- [justinho](https://github.com/JustinhoCHN)

### License

- For academic and non-commercial use only.
- For commercial use, please contact tensorlayer@gmail.com.