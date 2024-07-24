import numpy as np
import cv2
from PIL import ImageFont, ImageDraw, Image
import matplotlib.pyplot as plt

# Assuming 'image' is your original digital advertisement image
# Assuming 'masked_cropped_image' is the extracted button region after inpainting
# Replace with your new CTA text and the path to your Arial font file
new_cta_text = "Your New CTA Text"
font_path = "arial.ttf"
font_size = 40  # Adjust based on your needs
padding = 10  # Adjust padding as needed

# Load the font
font = ImageFont.truetype(font_path, font_size)

# Create a dummy image to measure the text size
dummy_img = Image.new('RGB', (1, 1))
draw = ImageDraw.Draw(dummy_img)
text_width, text_height = draw.textsize(new_cta_text, font=font)

# Calculate the new button size with padding
button_width = text_width + 2 * padding
button_height = text_height + 2 * padding

# Resize the masked_cropped_image to fit the new CTA text
original_button_height, original_button_width, _ = masked_cropped_image.shape
scale_x = button_width / original_button_width
scale_y = button_height / original_button_height
scale = max(scale_x, scale_y)  # Scale proportionally

new_button_width = int(original_button_width * scale)
new_button_height = int(original_button_height * scale)

resized_button_image = cv2.resize(masked_cropped_image, (new_button_width, new_button_height))

# Create a blank image with the same size as the original image
blank_image = np.zeros_like(image)

# Calculate the position to place the resized button
y_offset = (blank_image.shape[0] - new_button_height) // 2
x_offset = (blank_image.shape[1] - new_button_width) // 2

# Place the resized button onto the blank image
blank_image[y_offset:y_offset+new_button_height, x_offset:x_offset+new_button_width] = resized_button_image

# Draw the new CTA text onto the resized button
resized_button_pil = Image.fromarray(blank_image)
draw = ImageDraw.Draw(resized_button_pil)
text_x = x_offset + padding
text_y = y_offset + padding
draw.text((text_x, text_y), new_cta_text, font=font, fill="white")

# Convert the PIL image back to a NumPy array
final_image_with_cta = np.array(resized_button_pil)

# Optionally, visualize the final image with the new CTA
plt.figure(figsize=(10, 10))
plt.imshow(cv2.cvtColor(final_image_with_cta, cv2.COLOR_BGR2RGB))
plt.axis('off')
plt.show()
