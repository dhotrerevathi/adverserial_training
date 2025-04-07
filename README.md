# Evasion Attacks and Adversarial Training on Machine Learning models

White-Box Evasion Attacks and Adversarial Training Defense

Introduction

This report presents the implementation and analysis of white-box evasion attacks against deep learning-based classification models and the effectiveness of adversarial training as a defense mechanism. The experiments were conducted using the German Traffic Sign Recognition Benchmark (GTSRB) dataset, which contains approximately 51,000 images of 43 different traffic sign classes
- Training samples: 39209
- Testing samples: 12630

![image](https://github.com/user-attachments/assets/12f9ab04-53a4-49eb-92a9-af4941bfadbd)

Figure 1. Images per class in the GTSRB dataset



### Part 1: Evasion Attacks

## Train Two Deep-Learning Classification Models

Two deep-learning models were implemented to classify the GTSRB dataset: VGG16 and ResNet50. Both models were initialized with pre-trained weights from ImageNet 

**Performance Analysis**


![image](https://github.com/user-attachments/assets/c57e1ed2-e10c-49ce-af26-7fd985a0f2fe)

Table 1: Classification accuracy of the models on the GTSRB dataset

**Model Architecture and Implementation**

VGG16 Model: 


![image](https://github.com/user-attachments/assets/5837f5fc-f95f-4472-ae1c-be265727dafc)
 
Fig 2: Loss and accuracy plots for VGG16 Model


ResNet50 Model: 

 ![image](https://github.com/user-attachments/assets/4b1589d1-a240-4822-8975-7773e2dccf6b)

Fig 3: Loss and accuracy plots for Resnet50 Model


Classification accuracy of the models on clean and adversarial images.

In this task, two common white-box evasion attacks against the trained deep-learning models are implemented: 
1. Fast Gradient Sign Method (FGSM)
2. Projected Gradient Descent (PGD)

The attacks were applied to the first 1,000 images of the test set with various perturbation magnitudes

(ε): [1/255, 2/255, 3/255, 4/255, 5/255, 8/255, 10/255, 20/255, 50/255, 80/255]


![image](https://github.com/user-attachments/assets/e8d99a29-b3df-404c-9aa8-e9bbd7d8f8b7)

Table 2: Classification accuracy of the models on clean and adversarial images.


Accuracy vs Perturbation Plots for both models

  
![image](https://github.com/user-attachments/assets/e84eca6e-e53c-41f9-9574-c5a994eb11b8)
  

![image](https://github.com/user-attachments/assets/004fb0c9-7663-48fb-9907-cf24a0dd09c2)

Fig 4: Pots of accuracy versus perturbation for VGG 16 and ResNet50

### Analysis of Results:

1.	Model Vulnerability to Adversarial Attacks

Both VGG16 and ResNet50 models demonstrate significant vulnerability to adversarial attacks, with accuracy dropping substantially as perturbation magnitude increases. At ε=10/255, VGG16's accuracy drops to 15.5% against FGSM attacks and 8.5% against PGD attacks, representing accuracy reductions of 70.6% and 77.6%, respectively, from clean image performance.

2.	Comparison Between Attack Methods

PGD consistently outperforms FGSM in attack effectiveness across both models. For VGG16, PGD reduces accuracy to 8.5% at ε=10/255 compared to FGSM's 15.5%. This aligns with theoretical expectations since PGD performs iterative optimization to find more optimal adversarial perturbations, while FGSM makes only a single step in the gradient direction.

3.	Model Architecture Differences

ResNet50 shows interesting patterns of robustness different from VGG16: Against FGSM attacks, ResNet50 maintains substantially higher accuracy (49% vs. 15.5% for VGG16 at ε=10/255). However, against PGD attacks, ResNet50 seems particularly vulnerable, especially at lower perturbation levels (15.5% accuracy at ε=1/255 compared to VGG16's 61.6%). This differential response suggests that ResNet50's skip connections may provide robustness against single-step attacks but potentially create unique vulnerability patterns against iterative attacks.

4.	Critical Perturbation Thresholds

Both models exhibit critical thresholds where accuracy drops precipitously: For VGG16, accuracy decreases relatively linearly until around ε=5/255, after which the decline slows. For ResNet50 with FGSM attacks, there's a dramatic drop between ε=5/255 (60.8%) and ε=10/255 (49%). These thresholds have important implications for defensive strategies, representing the point where model performance becomes unacceptably compromised.

5.	Perceptibility vs. Attack Success:

One crucial observation is that perturbations of ε=5/255 are typically barely perceptible to humans, yet they reduce model accuracy significantly (to 26.6% for VGG16-FGSM and 16.4% for VGG16-PGD). This highlights the practical danger of adversarial attacks in real-world settings where humans might not notice the manipulations.


Meaning of the Perturbation Magnitude ε=7/255

The perturbation magnitude ε=7/255 has a specific technical meaning in the context of adversarial attacks:

L-infinity Norm Constraint: The epsilon value represents the maximum allowable change to any pixel value in the image, measured using the L∞ (L-infinity) norm. This means that no pixel can be changed by more than ε in either direction.

Normalized Pixel Values: Since pixel values in our implementation are normalized to the range [0,1], ε=7/255 means each pixel can be perturbed by at most 7/255 ≈ 0.0275 in either direction. In the original 8-bit pixel value range [0,255], this would correspond to a maximum change of 7 intensity levels per pixel.

Element-wise Application: The perturbation is applied element-wise to the input image. For an RGB image, each color channel of each pixel can be independently perturbed by at most ε, as determined by the gradient direction that maximizes the loss function.

Bounded Perturbation: The attack ensures that all perturbed pixel values remain within valid bounds [0,1], through clipping operations. This preserves the visual plausibility of the image while maximizing the attack efficacy.

Imperceptibility Threshold: A perturbation of ε=7/255 is generally considered to be at the boundary of human perceptibility for most natural images. Smaller values (like 1/255 or 3/255) are typically imperceptible, while larger values may introduce visible artifacts.

The L∞ norm constraint is specifically chosen in adversarial attacks because it allows for subtle, distributed changes across the entire image rather than concentrated changes in a few pixels, making the perturbations harder to detect visually while still being highly effective at fooling the model.


Figures of Adversarial Images:
 
![image](https://github.com/user-attachments/assets/66217add-4f23-49fb-8e13-f3895ee3605d)
 
### Analysis of Targeted Adversarial Attacks on Traffic Sign Recognition Models
VGG16 Model Analysis

 ![image](https://github.com/user-attachments/assets/c5efa865-d872-4f92-8791-abd7e6fcaa92)

Fig 6: VGG16 Teargeted Attack Results

1.	PGD Attack Performance on VGG16

The PGD attack on the VGG16 model showed remarkable effectiveness in targeted misclassification:

At ε=1/255 (0.0039): PGD already reduced Stop sign classification accuracy from 89.63% to 65.56%, with 20.37% of images successfully misclassified as Speed Limit 30 signs.

At ε=3/255 (0.0118): Stop sign accuracy dropped dramatically to 11.85%, while target class success rate rose to 83.33%.

At ε=5/255 (0.0196): The attack achieved perfect success with 0% Stop sign accuracy and 100% misclassification as Speed Limit 30.

For all larger perturbations (ε≥5/255): The attack maintained 100% targeted misclassification success.


2.	FGSM Attack Performance on VGG16

The FGSM attack on VGG16 was less effective than PGD:

At ε=1/255 (0.0039): FGSM reduced Stop sign accuracy to 64.44%, similar to PGD, but with only 10.74% target success rate.

At ε=3/255 (0.0118): Stop sign accuracy dropped to 7.41%, but target success reached only 58.89%.

The maximum target success rate of 62.22% occurred at ε=5/255 (0.0196).

For larger perturbations, the target success rate actually decreased, dropping to near-zero at ε=50/255 (0.1961).


ResNet50 Model Analysis

 ![image](https://github.com/user-attachments/assets/d1111f32-2107-4d68-a5cb-79f470caaed7)

Fig 7: ResNet50 Teargeted Attack Results


1.	PGD Attack Performance on ResNet50

The ResNet50 model proved extremely vulnerable to targeted PGD attacks:
Even at the smallest perturbation ε=1/255 (0.0039): PGD completely eliminated Stop sign recognition (0% accuracy) and achieved 100% misclassification as Speed Limit 30 signs.
This perfect targeted attack success continued across all perturbation levels.

2.	FGSM Attack Performance on ResNet50

The FGSM attack on ResNet50 showed a completely different pattern:
At lower perturbations, FGSM paradoxically improved Stop sign recognition, increasing accuracy from 84.44% to 92.96% at ε=8/255 (0.0314).
The attack never successfully achieved targeted misclassification, with the maximum success rate being only 8.15% at ε=1/255 (0.0039).
At higher perturbations (ε≥20/255), FGSM caused misclassification but not to the target class.


Comparative Analysis

1.	Attack Method Effectiveness:
-	PGD consistently outperformed FGSM for targeted attacks across both models.
-	PGD converged to higher success rates with smaller perturbations.
-	FGSM showed inconsistent behavior, especially on ResNet50 where it actually improved classification at some perturbation levels.

2.	Model Vulnerability:

ResNet50 showed extreme vulnerability to PGD attacks, with 100% target success at all perturbation levels.

VGG16 demonstrated more gradual degradation, requiring ε=5/255 to reach 100% target success with PGD.

Both models showed stronger resistance to FGSM attacks.


3.	Perturbation Level Impact:

For PGD: Smaller perturbations (ε=1/255 to 5/255) showed the most dramatic changes in effectiveness.

For FGSM: The relationship between perturbation magnitude and attack success was non-monotonic, with effectiveness sometimes decreasing at higher perturbation levels.

4.	Attack Efficiency:

PGD achieved more reliable targeted misclassification with fewer visible artifacts (lower perturbation levels).

FGSM, despite being computationally simpler, could not achieve high targeted success rates, particularly on ResNet50.

5.	Optimization Strategy Importance:

PGD's iterative optimization approach proved crucial for targeted attacks, showing that simply following the sign of the gradient (as in FGSM) is insufficient for reliable targeted misclassification.

The stark performance difference between PGD and FGSM highlights the importance of attack optimization strategy.



### Classification accuracy for the Stop sign class and the target class on the PGD adversarially manipulated images:


![image](https://github.com/user-attachments/assets/170f065b-3729-4627-8717-2f8079d5ec1f)

Table 3. Classification accuracy of the model on adversarial original and target class images.

TARGETED ADVERSARIAL EXAMPLES

Targeted Attack Prediction Labels:
-	Class 14 (Stop) – 0
-	Speed 30  - 1


![image](https://github.com/user-attachments/assets/e69bcdbd-22b0-4741-a0d2-35cd4e77cd7b)

Fig 8: PGD Attack - Targeted Adversarial Examples with perturbations using VGG16 Model


![image](https://github.com/user-attachments/assets/8dba368d-f3f8-4ad9-aaed-9f53afcbfee3)

Fig 9: Targeted Adversarial Examples with perturbations using ResNet50 model

Implications and Conclusions

The results demonstrate that targeted adversarial attacks present a severe security concern for traffic sign recognition systems. PGD attacks can reliably cause specific misclassifications with minimal visual changes to the input images. This is particularly concerning for safety-critical applications like autonomous driving, where a Stop sign being misclassified as a Speed Limit sign could lead to dangerous scenarios.

The extreme vulnerability of ResNet50 to PGD attacks, despite its generally higher classification accuracy on clean images, suggests that model accuracy and adversarial robustness are not necessarily correlated. This highlights the need for specific adversarial defenses rather than improving model accuracy.

The significant performance gap between PGD and FGSM attacks underscores the importance of iterative optimization in crafting effective targeted adversarial examples. While FGSM may be sufficient for untargeted attacks (simply causing misclassification), targeted attacks require the more sophisticated approach PGD offers.

These findings emphasize the need for robust adversarial defenses in machine learning systems deployed in safety-critical environments, as even small, imperceptible perturbations can cause reliable and potentially dangerous misclassification.



