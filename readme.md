import os
import shutil
import matplotlib.pyplot as plt
from shutil import copyfile

def inpaint_image(image_path, mask_path, output_dir='/content/output'):
    # Prepare directories
    shutil.rmtree('./data_for_prediction', ignore_errors=True)
    os.makedirs('data_for_prediction', exist_ok=True)

    # Copy image and mask to the prediction directory
    image_name = os.path.basename(image_path)
    copyfile(image_path, f'./data_for_prediction/{image_name}')
    copyfile(mask_path, f'./data_for_prediction/mask.png')

    # Perform inpainting
    suffix = image_name.split('.')[-1]
    if suffix in ['jpeg', 'jpg', 'png']:
        !PYTHONPATH=. TORCH_HOME=$(pwd) python3 bin/predict.py model.path=$(pwd)/big-lama indir=$(pwd)/data_for_prediction outdir={output_dir} dataset.img_suffix=.{suffix} > /dev/null
    else:
        print(f'Error: unknown suffix .{suffix} use [.png, .jpeg, .jpg]')
    
    # Display the result
    output_image_path = f"{output_dir}/{image_name.split('.')[0]}_mask.png"
    plt.imshow(plt.imread(output_image_path))
    plt.axis('off')
    plt.title('Inpainting Result')
    plt.show()

# Example usage
image_path = 'path_to_image.jpg'  # Path to the image
mask_path = 'path_to_mask.png'    # Path to the corresponding mask
inpaint_image(image_path, mask_path)
