![Logo](https://github.com/Sharif-University-ESRLab/fall2024-embeddedml-benchmark/blob/main/Logo.jpg)

# A Benchmarking Framework for Machine Learning Algorithms on Embedded Devices

This project measures how well an STM32 microcontroller can recognize spoken commands. It checks how fast the device processes information, how much memory it uses, and how much power it consumes.

## Tools

**Hardware:**

- **STM32 Development Board:** We used the STM32F103R8 series.
- **Audio Input:** A microphone or pre-recorded audio samples for testing.

**Software:**

- **Development Tools:**
  - **STM32CubeIDE:** An integrated development environment for STM32 microcontrollers.
  - **STM32Cube.AI:** A tool to convert and optimize neural network models for STM32 devices.
- **Libraries:**
  - **TensorFlow Lite for Microcontrollers:** A lightweight version of TensorFlow designed for microcontroller environments.
- **Other Tools:**
  - **Python 3.x:** For scripting and model preparation.
  - **Git:** For version control.

## Implementation Details

**1. Model Preparation and Conversion**

- **Convert the Model to TensorFlow Lite Format:**
  - Use a script (e.g., `ModelConverter.py`) to convert your trained model into the TensorFlow Lite format (`.tflite`).

- **Validate the TensorFlow Lite Model:**
  - Use a validation script (e.g., `ValidateTFModel.py`) to ensure the converted model works correctly.

**2. Setting Up the STM32 Environment**

- **Install STM32CubeIDE:**
  - Download and install STM32CubeIDE from the [STMicroelectronics website](https://www.st.com/en/development-tools/stm32cubeide.html).

- **Install STM32Cube.AI:**
  - Within STM32CubeIDE, install the STM32Cube.AI plugin to enable model conversion and optimization features.

**3. Deploying the Model to STM32**

- **Convert the TensorFlow Lite Model to C Code:**
  - Open STM32CubeIDE and switch to the STM32Cube.AI perspective.
  - Select your `.tflite` model file.
  - STM32Cube.AI will generate optimized C code from the model.

- **Create a New STM32 Project:**
  - In STM32CubeIDE, create a new project for your specific STM32 board.
  - Add the generated C files from STM32Cube.AI to your project.
  - Configure necessary peripherals (e.g., ADC, I2S) for audio input.
  - Use the generated API to load the model and perform inference.

**4. Implementing the Benchmark Test**

- **Develop Inference Code:**
  - Write the main application code (e.g., in `runner.c`) to:
    - Capture audio data.
    - Preprocess the data as required by the model.
    - Run inference using the model.
    - Record performance metrics.

- **Measure Performance Metrics:**
  - **Inference Latency:** Measure the time taken to perform a single inference.
  - **Memory Usage:** Monitor RAM and Flash usage to ensure they are within the STM32's constraints.
  - **Power Consumption:** Use tools like ST-Link's power profiling feature or external hardware to assess power usage during inference.

**5. Running and Analyzing the Benchmark**

- **Flash the Firmware:**
  - Build the project in STM32CubeIDE to generate the firmware binary.
  - Connect the STM32 board and flash the firmware onto it.

- **Execute the Benchmark:**
  - Reset or power cycle the STM32 board to start running the benchmark.
  - Use a serial terminal (e.g., PuTTY, Tera Term) to view the printed inference times and predicted labels.

- **Analyze Data:**
  - **Inference Latency:** Calculate the average, minimum, and maximum inference times from the serial output.
  - **Memory Usage:** Check the compiled binary size and runtime memory usage.
  - **Power Consumption:** Review the power usage data collected during inference operations.

**Example Serial Output:**

```
Inference Time: 15 ms, Predicted Label: 3
Inference Time: 14 ms, Predicted Label: 7
Inference Time: 16 ms, Predicted Label: 2
...
```

## How to Run

**Build the Project:**

In STM32CubeIDE, build your project to generate the firmware binary.

**Flash the Firmware:**

Connect your STM32 board to your computer.

Use STM32CubeIDE to flash the generated firmware onto the board.

**Run the Benchmark:**

After flashing, reset or power cycle the STM32 board.

Open a serial terminal (e.g., PuTTY, Tera Term) and connect to the appropriate COM port to view the output.

## Results

After running the benchmark, you should observe output similar to the following in your serial terminal:

```
Inference Time: 15 ms, Predicted Label: 3
Inference Time: 14 ms, Predicted Label: 7
Inference Time: 16 ms, Predicted Label: 2
...
```

Analyze this data to assess your model's performance on the STM32 microcontroller.

As we can interpret from the table below, in this work, we assess five different scenarios, targeting different tasks in ML, like speech recognition, anomaly detection, image classification, and lastly, emotion detection in the domain of natural language processing.

![image](https://github.com/user-attachments/assets/4ce2b044-fa08-41f4-ad6d-e81e4e9fe802)

From the table, it can be understood that many developed TFLite models are well reached in the limited target of 64 KB memory, and only the LSTM structure exceeds this value because of its tokenizer.

### Emotion Detection

Regarding detecting whether a sentence in the domain of text has what type of emotion, we developed two different models: BERT and LSTM.

For the BERT model, we utilized the ParsBERT embedding space and defined a Dense-CNN classifier on top of it to be trained using our novel dataset. Below, the process of training and the value of accuracy and loss can be seen:

Accuracy over each iteration of training:

![image](https://github.com/user-attachments/assets/1c2ca0ca-fbb0-4278-8d85-df3baa9e6d42)

The value of loss for each iteration:

![image](https://github.com/user-attachments/assets/71506042-5d7a-4927-abd1-a2a34936bc99)

With the aid of the TFlite converter, we were able to reduce the size of the model to one-fourth of the initial size or, in other words, 162 MB, but this is beyond our limitation of the embedding system, so we developed an LSTM architecture in its place.

#### LSTM Model to Detect Emotion

For this task, we developed two distinct models, one regular one using a simple tokenizer by defining a dictionary of 2000 maximum vocab, and another using dynamic-learning rate and `sparse_categorical_crossentropy` loss. This model resulted in 82% and 89% accuracy, respectively, which is a promising result for 640 KB and 161 KB models.

Here, the confusion matrix of LSTM architecture can be seen:

![image](https://github.com/user-attachments/assets/fc1725a9-e641-4f85-9458-27b708a142d7)

And we have this result for enhanced architecture:

![image](https://github.com/user-attachments/assets/cc5c4c41-f421-43e2-b0c4-ca71192d8a28)

Furthermore, more detailed information regarding classification is included below:

```txt
 precision    recall  f1-score   support

           0       0.88      0.76      0.82       110
           1       0.81      0.84      0.83       102
           2       0.95      0.86      0.90        97
           3       0.90      0.97      0.94       104
           4       0.97      0.96      0.96        95
           5       0.88      0.98      0.93       100
           6       0.99      0.94      0.96       113
           7       0.98      0.92      0.95       104
           8       0.80      0.73      0.76       101
           9       0.99      1.00      1.00       103
          10       0.91      0.92      0.92       100
          11       0.71      0.85      0.77       105

    accuracy                           0.89      1234
   macro avg       0.90      0.89      0.89      1234
weighted avg       0.90      0.89      0.89      1234
```



## Dataset Gathering

One of our works' novel contributions is emotion detection dataset creation. For this dataset, we utilized GPT to generate sentences for each emotion, resulting in more than 6000 Farsi-labeled sentences across 12 different classes. For this reason, we used prompt engineering techniques to make sure that the generated sentences were valid and also unique.


## Related Links

- [STM32CubeIDE](https://www.st.com/en/development-tools/stm32cubeide.html)
- [STM32Cube.AI](https://www.st.com/en/development-tools/stm32cubeai.html)
- [TensorFlow Lite for Microcontrollers](https://www.tensorflow.org/lite/microcontrollers)

## Authors
- [Iman Mohammadi](https://github.com/Imanm02)
- [Shayan Salehi](https://github.com/ShayanSalehi81)
- [Armin Saghafian](https://github.com/ArminS03)

Special Thanks to [Ali Salesi](https://github.com/alisalc).
