def run_inpainting(self, image_name):
        suffix = image_name.split('.')[-1]
        if suffix in ['jpeg', 'jpg', 'png']:
            cmd = [
                'python3', 'bin/predict.py',
                f'model.path={self.model_path}',
                f'indir={self.prediction_dir}',
                f'outdir={self.output_dir}',
                f'dataset.img_suffix=.{suffix}'
            ]
            result = subprocess.run(cmd, stdout=subprocess.PIPE, stderr=subprocess.PIPE)
            if result.returncode != 0:
                print(f"Error during inpainting: {result.stderr.decode()}")
        else:
            print(f'Error: unknown suffix .{suffix}. Use [.png, .jpeg, .jpg]')
