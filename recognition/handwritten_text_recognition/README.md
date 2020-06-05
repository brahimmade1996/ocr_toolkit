# Handwritten Text Recognition (OCR) with MXNet Gluon 
These notebooks have been created by [Jonathan Chung](https://github.com/jonomon), as part of his internship as Applied Scientist @ Amazon AI, in collaboration with [Thomas Delteil](https://github.com/ThomasDelteil) who built the original prototype.
## Usage
Parameters:

    Handwritten Text Recognization in one step
    image: input image in numpy.array object that includes handwritten text
    form_size: possible form size
    device:
    If it is None:
        If num_device==1: uses gpu if there is any gpu
        else: uses num_device gpu if there is any gpu
    If it is 'auto':
        If num_device==1: uses gpu if there is any gpu
        else: uses num_device gpu if there is any gpu
    if it is 'cpu': uses one, num_device-1 indexed cpu
    if it is 'gpu': uses one, num_device-1 indexed gpu
    num_device: number of device that module running on.
    cropping detected text area
    ScliteHelperPATH: Tool that helps to get quantitative results. https://github.com/usnistgov/SCTK
    show: Show plot if show=True. default; show=False
    is_test: If it is True than activate SCTK tool to get quantative results.
recognize Usage:

    image = mx.image.imread("tests/TurkishHandwritten/elyaz2.jpeg")
    image = image.asnumpy()
    recog = recognize(image, device=device)
    result = recog()
recognize class returns:
    
    results = {
    'predicted_text_area': predicted_text_area,
    'croped_image': croped_image,
    'predicted_bb': predicted_bb,
    'line_images_array': line_images_array,
    'character_probs': character_probs,
    'decoded': decoded
    }
recognize_test Usage:

    htr_test = recognize_test(show=True, device=device)
    result = htr_test()
recognize_IAM_random_test Usage:

    IAM_recog = recognize_IAM_random_test(device)
    result = IAM_recog()
recognize_IAM_random_test Usage:

    IAM_recog = recognize_IAM_random_test(device)
    result = IAM_recog()
## Requirements:
    scikit_image
    scipy
    matplotlib
    tqdm
    mxnet_cu102mkl
    pandas
    nltk
    mxboard
    numpy
    gluonnlp
    leven
    ipython
    mxnet
    mxboard
    Pillow
    pyenchant
    scikit-image
    sympound
    weighted_levenshtein
    hnswlib
    pybind11
    setuptools
    pdoc3
    pprint
    """

## Setup

`git clone https://github.com/develooper1994/handwritten-text-recognition --recursive`

You need to install SCLITE for WER evaluation
You can follow the following bash script from this folder:

```bash or batch
cd ..
git clone https://github.com/usnistgov/SCTK
cd SCTK
export CXXFLAGS="-std=c++11" && make config
make all
make check
make install
make doc
cd -
```

You also need hsnwlib

```bash or batch
pip install pybind11 numpy setuptools
pip install hnswlib
```

## Overview 

![](https://cdn-images-1.medium.com/max/1000/1*nJ-ePgwhOjOhFH3lJuSuFA.png)

The pipeline is composed of 3 steps:
- Detecting the handwritten area in a form [[blog post](https://medium.com/apache-mxnet/page-segmentation-with-gluon-dcb4e5955e2)], [[jupyter notebook](https://github.com/awslabs/handwritten-text-recognition-for-apache-mxnet/blob/master/1_b_paragraph_segmentation_dcnn.ipynb)], [[python script](https://github.com/awslabs/handwritten-text-recognition-for-apache-mxnet/blob/master/ocr/scripts/paragraph_segmentation_dcnn.py)]
- Detecting lines of handwritten texts [[blog post](https://medium.com/apache-mxnet/handwriting-ocr-line-segmentation-with-gluon-7af419f3a3d8)], [[jupyter notebook](https://github.com/awslabs/handwritten-text-recognition-for-apache-mxnet/blob/master/2_line_word_segmentation.ipynb)], [[python script](https://github.com/awslabs/handwritten-text-recognition-for-apache-mxnet/blob/master/word_and_line_segmentation.py)]
- Recognising characters and applying a language model to correct errors. [[blog post](https://medium.com/apache-mxnet/handwriting-ocr-handwriting-recognition-and-language-modeling-with-mxnet-gluon-4c7165788c67)], [[jupyter notebook](https://github.com/awslabs/handwritten-text-recognition-for-apache-mxnet/blob/master/3_handwriting_recognition.ipynb)], [[python script](https://github.com/awslabs/handwritten-text-recognition-for-apache-mxnet/blob/master/ocr/scripts/handwriting_line_recognition.py)]

The entire inference pipeline can be found in this [notebook](https://github.com/awslabs/handwritten-text-recognition-for-apache-mxnet/blob/master/0_handwriting_ocr.ipynb). See the *pretrained models* section for the pretrained models.

A recorded talk detailing the approach is available on youtube. [[video](https://www.youtube.com/watch?v=xDcOdif4lj0)]

The corresponding slides are available on slideshare. [[slides](https://www.slideshare.net/apachemxnet/ocr-with-mxnet-gluon)]

## Pretrained models:

You can get the models by running `python get_models.py`:

## Sample results

![](https://cdn-images-1.medium.com/max/2000/1*8lnqqlqomgdGshJB12dW1Q.png)

The greedy, lexicon search, and beam search outputs present similar and reasonable predictions for the selected examples. In Figure 6, interesting examples are presented. The first line of Figure 6 show cases where the lexicon search algorithm provided fixes that corrected the words. In the top example, “tovely” (as it was written) was corrected “lovely” and “woved” was corrected to “waved”. In addition, the beam search output corrected “a” into “all”, however it missed a space between “lovely” and “things”. In the second example, “selt” was converted to “salt” with the lexicon search output. However, “selt” was erroneously converted to “self” in the beam search output. Therefore, in this example, beam search performed worse. In the third example, none of the three methods significantly provided comprehensible results. Finally, in the forth example, the lexicon search algorithm incorrectly converted “forhim” into “forum”, however the beam search algorithm correctly identified “for him”.

## Dataset:
* To use test_iam_dataset.ipynb, create credentials.json using credentials.json.example and editing the appropriate field. The username and password can be obtained from http://www.fki.inf.unibe.ch/DBs/iamDB/iLogin/index.php.

* **It is recommended to use an instance with 32GB+ RAM and 100GB disk size, a GPU is also recommended. A p3.2xlarge would be the recommended starter instance on AWS for this project**

## Appendix

### 1) Handwritten area

#####  Model architecture

![](https://cdn-images-1.medium.com/max/1000/1*AggJmOXhjSySPf_4rPk4FA.png)

##### Results

![](https://cdn-images-1.medium.com/max/800/1*HEb82jJp93I0EFgYlJhfAw.png) 

### 2) Line Detection

##### Model architecture

![](https://cdn-images-1.medium.com/max/800/1*jMkO7hy-1f0ZFHT3S2iH0Q.png)

##### Results

![](https://cdn-images-1.medium.com/max/1000/1*JJGwLXJL-bV7zsfrfw84ew.png)

### 3) Handwritten text recognition

##### Model architecture

![](https://cdn-images-1.medium.com/max/800/1*JTbCUnKgAySN--zJqzqy0Q.png)

##### Results

![](https://cdn-images-1.medium.com/max/2000/1*8lnqqlqomgdGshJB12dW1Q.png)

##### Requirement
At leats 11GB VRAM GPU or 11GB space on RAM.

### Thanks to
https://pythonawesome.com/handwritten-text-recognition-ocr-with-mxnet-gluon/
https://www.youtube.com/watch?v=xDcOdif4lj0&t=1281s
https://github.com/awslabs/handwritten-text-recognition-for-apache-mxnet

### Should Look at
https://rrc.cvc.uab.es/
https://www.pyimagesearch.com/2018/08/20/opencv-text-detection-east-text-detector/

### Further steps
    Handwriting reading process is developing gradually.
    - Read "born digital"
    - Read printed "born digital"
    - Read handwritten by tablet or any input device
    - Handwritten recorded under perfect conditions
    - Ability to read horizontally
    - Crowded historical texts
    - Signage etc. perception
    - .......
    - Extract information from video taken in the wilderness
     or in the city (without any prior correction, like human)