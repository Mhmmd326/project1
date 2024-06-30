# project1
from Crypto.Cipher import AES
import time
import numpy as np

def aes_encryption(data, key):
    # Initialize the AES cipher object with the key
    cipher = AES.new(key, AES.MODE_ECB)

    # Encrypt the data
    start_time = time.perf_counter()
    cipher_text = b""
    for i in range(0, len(data), 16):
        block = data[i:i+16]
        if len(block) < 16:
            block += b"\x00" * (16 - len(block))
        encrypted_block = cipher.encrypt(block)
        cipher_text += encrypted_block
    end_time = time.perf_counter()

    # Measure the time taken for encryption
    encryption_time = end_time - start_time

    return cipher_text, encryption_time

def perform_timing_analysis(iterations=100):
    data = b"Hello, world!" * 10  # Increased data size for better analysis
    key = b'Sixteen byte key'

    # Collect timing data without delay
    times_without_delay = []
    for _ in range(iterations):
        _, encryption_time = aes_encryption(data, key)
        times_without_delay.append(encryption_time)

    # Collect timing data with delay
    times_with_delay = []
    for _ in range(iterations):
        _, encryption_time = aes_encryption(data, key)
        # Simulating delay manually for analysis
        time.sleep(0.01)  # Adding a small delay for each encryption
        times_with_delay.append(encryption_time + 0.01)

    # Calculate average times
    mean_without_delay = np.mean(times_without_delay)
    mean_with_delay = np.mean(times_with_delay)

    # Calculate the security probability
    security_probability = 1 - (mean_without_delay / mean_with_delay)
    security_percentage = max(0, security_probability) * 100

    print("Average Encryption Time without Delay:", mean_without_delay, "seconds")
    print("Average Encryption Time with Delay:", mean_with_delay, "seconds")
    print("Security Probability against Timing Attacks:", security_percentage, "%")

if __name__ == "__main__":
    perform_timing_analysis()
