import numpy as np
import cv2
from PIL import ImageFont, ImageDraw, Image
import matplotlib.pyplot as plt

def wrap_text(text, font, max_width):
    """
    Wrap text for the given font and max_width.
    """
    lines = []
    words = text.split()
    line = []
    
    for word in words:
        line_width, _ = draw.textsize(' '.join(line + [word]), font=font)
        if line_width <= max_width:
            line.append(word)
        else:
            lines.append(' '.join(line))
            line = [word]
    lines.append(' '.join(line))
    
    return lines

# Load the original image
# image = cv2.imread('path_to_your_image.jpg')  # Uncomment and adjust if needed

# Define the new CTA text and font details
new_cta_text = "Your New CTA Text"
font_path = "arial.ttf"
font_size = 40
padding = 10
max_width_factor = 1.5  # Maximum button width as a factor of the original width

# Load the font
font = ImageFont.truetype(font_path, font_size)

# Calculate initial button size
dummy_img = Image.new('RGB', (1, 1))
draw = ImageDraw.Draw(dummy_img)
initial_button_width, initial_button_height = draw.textsize(new_cta_text, font=font)
max_button_width = initial_button_width * max_width_factor

# Wrap text if necessary
wrapped_text = wrap_text(new_cta_text, font, max_button_width - 2 * padding)
wrapped_text_height = sum(draw.textsize(line, font=font)[1] for line in wrapped_text)
button_width = min(max_button_width, initial_button_width)
button_height = wrapped_text_height + 2 * padding

# Resize the masked_cropped_image to fit the new CTA text
original_button_height, original_button_width, _ = masked_cropped_image.shape
scale_x = button_width / original_button_width
scale_y = button_height / original_button_height
scale = max(scale_x, scale_y)  # Scale proportionally

new_button_width = int(original_button_width * scale)
new_button_height = int(original_button_height * scale)

# Resize the masked button image
resized_button_image = cv2.resize(masked_cropped_image, (new_button_width, new_button_height))

# Convert OpenCV image to PIL for text drawing
resized_button_pil = Image.fromarray(cv2.cvtColor(resized_button_image, cv2.COLOR_BGR2RGB))
draw = ImageDraw.Draw(resized_button_pil)

# Draw the wrapped text onto the resized button
y = padding
for line in wrapped_text:
    draw.text((padding, y), line, font=font, fill="white")
    y += draw.textsize(line, font=font)[1]

# Convert the PIL image back to OpenCV format
final_button_with_cta = cv2.cvtColor(np.array(resized_button_pil), cv2.COLOR_RGB2BGR)

# Define the original button's position based on the input points
input_point = np.array([[160, 50], [153, 100]])
min_y, max_y = input_point[:, 1].min(), input_point[:, 1].max()
original_y = min_y

# Calculate the position to place the resized button
x_offset = min_x
y_offset = original_y

# Ensure button does not go out of bounds
y_offset = max(y_offset, 0)
x_offset = max(x_offset, 0)

# Adjust position to avoid corners
x_offset = min(x_offset, image.shape[1] - new_button_width)
y_offset = min(y_offset, image.shape[0] - new_button_height)

# Create a mask for inpainting
inpaint_mask = np.zeros(image.shape[:2], dtype=np.uint8)
inpaint_mask[y_offset:y_offset+new_button_height, x_offset:x_offset+new_button_width] = 1

# Inpaint the original button area
inpainted_image = cv2.inpaint(image, inpaint_mask, 3, cv2.INPAINT_TELEA)

# Create a blank image with the same size as the original image
blank_image = np.zeros_like(image)

# Place the resized button onto the blank image at the correct position
blank_image[y_offset:y_offset+new_button_height, x_offset:x_offset+new_button_width] = final_button_with_cta

# Create a mask for the button area
button_mask = np.zeros_like(image, dtype=np.uint8)
button_mask[y_offset:y_offset+new_button_height, x_offset:x_offset+new_button_width] = final_button_with_cta

# Convert mask to grayscale and threshold
button_mask_gray = cv2.cvtColor(button_mask, cv2.COLOR_BGR2GRAY)
_, button_mask_binary = cv2.threshold(button_mask_gray, 1, 255, cv2.THRESH_BINARY)

# Prepare the button overlay using the mask
button_overlay = np.zeros_like(image)
button_overlay[y_offset:y_offset+new_button_height, x_offset:x_offset+new_button_width] = final_button_with_cta

# Use bitwise operations to combine the inpainted image with the button overlay
final_image_with_button = cv2.bitwise_and(inpainted_image, inpainted_image, mask=cv2.bitwise_not(button_mask_binary))
final_image_with_button = cv2.add(final_image_with_button, button_overlay)

# Visualize the final image with the new CTA
plt.figure(figsize=(10, 10))
plt.imshow(cv2.cvtColor(final_image_with_button, cv2.COLOR_BGR2RGB))
plt.axis('off')
plt.show()
