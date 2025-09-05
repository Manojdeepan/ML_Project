Deep Steganography using MobileNet (ML Project)
https://img.shields.io/badge/Python-3.x-blue.svg
https://img.shields.io/badge/TensorFlow-DeepLearning-orange.svg
https://img.shields.io/badge/OpenCV-ImageProcessing-green.svg
https://img.shields.io/badge/Status-Active-success.svg

Project Overview
This project implements a deep learning-based steganography system that hides and extracts secret text messages inside images.
It uses MobileNet as a feature extractor to locate low-information regions (smooth areas in the image), where encrypted text can be embedded securely.

The system also employs XOR-based encryption to secure hidden text, ensuring that even if extracted, the data is unreadable without the correct passphrase.

Key Features
Deep Feature Extraction: Uses MobileNet to identify low-information regions suitable for data embedding.

XOR Encryption: Encrypts and decrypts text using a passphrase before embedding.

Steganography Embedding: Hides text in image pixel LSBs (Least Significant Bits) within low-info regions.

Delimiter-Based Extraction: Uses a unique delimiter to detect the end of hidden text.

Cross-Platform Support: Works with Google Colab for easy experimentation.

Repository Contents
deep_steganography_mobilenet.py – Main implementation script.

README.md – Project documentation.

Installation
Clone or download this repository.

Install dependencies:

bash
pip install tensorflow opencv-python pillow numpy
Workflow
1. Encryption (Embedding):
User provides an image and a text file.

Text is encrypted with XOR using a passphrase.

Binary text is embedded in low-info regions of the image.

Output is a new image containing the hidden message.

2. Decryption (Extraction):
User provides the stego-image and the same passphrase.

Binary data is extracted from low-info regions.

XOR decryption retrieves the hidden message.

Usage
Run the script:
bash
python deep_steganography_mobilenet.py
Example Usage:
Encryption Example:

text
Enter the passphrase for encryption/decryption: mypass123
Do you want to encrypt or decrypt the image? (Enter 'encrypt' or 'decrypt'): encrypt
Input: cover_image.png, secret.txt

Output: output_image_with_text.png (with hidden message)

Decryption Example:

text
Enter the passphrase for encryption/decryption: mypass123
Do you want to encrypt or decrypt the image? (Enter 'encrypt' or 'decrypt'): decrypt
Input: output_image_with_text.png

Output: extracted_text.txt (decrypted secret message)

Security Notes
Embedding capacity depends on available low-information pixels

If the text is too long for the chosen image, an error will be raised

Passphrase must be the same during encryption and decryption for successful retrieval

The system uses XOR encryption which provides basic security (consider stronger encryption for highly sensitive data)

Tools and Technologies
Python 3.x

TensorFlow/Keras – MobileNet feature extraction

OpenCV – Image processing and LSB manipulation

Pillow (PIL) – Image handling

NumPy – Matrix operations

Author
Manoj Deepan M
