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
border_color = (255, 0, 0)  # Red border color
border_width = 5  # Border width in pixels
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

# Draw the border inside the button
for i in range(border_width):
    draw.rectangle(
        [i, i, new_button_width - i - 1, new_button_height - i - 1],
        outline=border_color
    )

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

# Create a mask for inpainting the original button area
button_mask = np.zeros(image.shape[:2], dtype=np.uint8)
button_mask[y_offset:y_offset+original_button_height, x_offset:x_offset+original_button_width] = 1

# Inpaint the original button area
inpainted_image = cv2.inpaint(image, button_mask, inpaintRadius=3, flags=cv2.INPAINT_TELEA)

# Overlay the new button onto the inpainted image
final_image_with_button = inpainted_image.copy()
final_image_with_button[y_offset:y_offset+new_button_height, x_offset:x_offset+new_button_width] = final_button_with_cta

# Visualize the final image with the new CTA
plt.figure(figsize=(10, 10))
plt.imshow(cv2.cvtColor(final_image_with_button, cv2.COLOR_BGR2RGB))
plt.axis('off')
plt.show()

# Generate the button without a border for comparison
resized_button_pil_no_border = Image.fromarray(cv2.cvtColor(resized_button_image, cv2.COLOR_BGR2RGB))
draw_no_border = ImageDraw.Draw(resized_button_pil_no_border)
y = padding
for line in wrapped_text:
    draw_no_border.text((padding, y), line, font=font, fill="white")
    y += draw.textsize(line, font=font)[1]
final_button_no_border = cv2.cvtColor(np.array(resized_button_pil_no_border), cv2.COLOR_RGB2BGR)

# Overlay the new button without a border onto the inpainted image
final_image_with_button_no_border = inpainted_image.copy()
final_image_with_button_no_border[y_offset:y_offset+new_button_height, x_offset:x_offset+new_button_width] = final_button_no_border

# Visualize the final image with the new CTA without border
plt.figure(figsize=(10, 10))
plt.imshow(cv2.cvtColor(final_image_with_button_no_border, cv2.COLOR_BGR2RGB))
plt.axis('off')
plt.show()
