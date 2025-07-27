# Install necessary libraries
!pip install tensorflow opencv-python-headless pillow

from tensorflow.keras.applications import MobileNet
from tensorflow.keras.applications.mobilenet import preprocess_input
from tensorflow.keras.preprocessing.image import img_to_array
from google.colab import files
from PIL import Image
import numpy as np
import cv2

# Load MobileNet for feature extraction (used to find low-information regions)
mobilenet = MobileNet(weights='imagenet', include_top=False, input_shape=(224, 224, 3))

# XOR encryption/decryption function
def xor_encrypt_decrypt(text, passphrase):
    return ''.join(chr(ord(char) ^ ord(passphrase[i % len(passphrase)])) for i, char in enumerate(text))

# Find low-information regions in the image using MobileNet feature extraction
def find_low_info_regions(image_path):
    img = Image.open(image_path).convert('RGB').resize((224, 224))
    img_array = preprocess_input(img_to_array(img)[np.newaxis, ...])
    features = mobilenet.predict(img_array)
    feature_map = np.mean(features[0], axis=-1)

    # Resize feature map to match original image dimensions
    original_img = Image.open(image_path).convert('RGB')
    low_info_mask = cv2.resize(feature_map, (original_img.width, original_img.height))

    # Threshold to mark low-information (smooth) regions
    return low_info_mask < np.median(low_info_mask)

# Embed encrypted text into image
def embed_text_in_image(image_path, text, passphrase, output_path):
    encrypted_text = xor_encrypt_decrypt(text, passphrase)
    binary_text = ''.join(format(ord(char), '08b') for char in encrypted_text) + '1111111111111110'  # Delimiter

    img = Image.open(image_path).convert('RGB')
    img_array = np.array(img)
    low_info_mask = find_low_info_regions(image_path)

    if len(binary_text) > np.sum(low_info_mask) * 3:
        raise ValueError("Text too long to embed in the selected regions.")

    index = 0
    for y in range(img_array.shape[0]):
        for x in range(img_array.shape[1]):
            if low_info_mask[y, x]:  # Only embed in low-info regions
                for channel in range(3):  # Iterate through RGB channels
                    if index < len(binary_text):
                        img_array[y, x, channel] = (img_array[y, x, channel] & ~1) | int(binary_text[index])
                        index += 1
                    if index >= len(binary_text):
                        break
            if index >= len(binary_text):
                break
        if index >= len(binary_text):
            break

    encoded_img = Image.fromarray(img_array)
    encoded_img.save(output_path)
    print(f"Text successfully embedded into {output_path}.")
    files.download(output_path)

# Extract and decrypt text from image
def extract_text_from_image(image_path, passphrase):
    img = Image.open(image_path).convert('RGB')
    img_array = np.array(img)
    binary_text = ''

    for y in range(img_array.shape[0]):
        for x in range(img_array.shape[1]):
            for channel in range(3):  # Iterate through RGB channels
                binary_text += str(img_array[y, x, channel] & 1)

    # Split into 8-bit chunks and stop at the delimiter
    all_bytes = [binary_text[i:i + 8] for i in range(0, len(binary_text), 8)]
    encrypted_text = ''
    for byte in all_bytes:
        if byte == '11111111':
            break
        encrypted_text += chr(int(byte, 2))

    decrypted_text = xor_encrypt_decrypt(encrypted_text, passphrase)
    print(f"Decrypted Text: {decrypted_text}")
    with open("extracted_text.txt", "w") as file:
        file.write(decrypted_text)
    files.download("extracted_text.txt")

# Main function to handle encryption and decryption options
def run():
    passphrase = input("Enter the passphrase for encryption/decryption: ")
    operation = input("Do you want to encrypt or decrypt the image? (Enter 'encrypt' or 'decrypt'): ").lower()

    if operation == 'encrypt':
        print("Upload the image file:")
        uploaded_image = files.upload()
        input_image_path = next(iter(uploaded_image))

        print("Upload the text file:")
        uploaded_text_file = files.upload()
        text_file_path = next(iter(uploaded_text_file))

        with open(text_file_path, 'r') as file:
            embedded_text = file.read()

        output_image_path = "output_image_with_text.png"
        embed_text_in_image(input_image_path, embedded_text, passphrase, output_image_path)

    elif operation == 'decrypt':
        print("Upload the image file:")
        uploaded_image = files.upload()
        input_image_path = next(iter(uploaded_image))

        extract_text_from_image(input_image_path, passphrase)

    else:
        print("Invalid operation. Please enter 'encrypt' or 'decrypt'.")

# Run the program
run()
