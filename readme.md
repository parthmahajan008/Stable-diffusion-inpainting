import numpy as np
import matplotlib.pyplot as plt

# Assuming masks contains the predicted mask
mask_region = masks[0]  # Get the mask for the region of interest

# Find the bounding box of the mask
y_indices, x_indices = np.where(mask_region)
min_x, max_x = x_indices.min(), x_indices.max()
min_y, max_y = y_indices.min(), y_indices.max()

# Crop the image using the bounding box
cropped_image = image[min_y:max_y+1, min_x:max_x+1]
cropped_mask = mask_region[min_y:max_y+1, min_x:max_x+1]

# Apply the mask to the cropped image
masked_cropped_image = np.zeros_like(cropped_image)
masked_cropped_image[cropped_mask == 1] = cropped_image[cropped_mask == 1]

# Optionally, visualize the cropped masked image
plt.figure(figsize=(10, 10))
plt.imshow(masked_cropped_image)
plt.axis('off')
plt.show()
