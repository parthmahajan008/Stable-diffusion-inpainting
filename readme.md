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
