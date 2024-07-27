Creative Mutation
Setup Instructions
Devcontainer Setup
Open the project folder in the devcontainer.
The devcontainer bootup will run all necessary installation scripts.
llama3:70b Setup for Alternate CTA Generation
Start the llama server:
bash
Copy code
ollama serve
Pull the llama3:70b model:
bash
Copy code
ollama pull llama3:70b
Google Cloud Vision Setup
Ensure you have a project in Google Cloud with the Cloud Vision API enabled. Follow the setup instructions here.
Authenticate with Google Cloud:
bash
Copy code
gcloud auth application-default login
Run the Workflow
Execute the main workflow notebook:
bash
Copy code
run notebooks/cta_workflow.ipynb
Finetuning the Model
Run the finetuning notebook:
bash
Copy code
run notebooks/unsloth_finetuning.ipynb
Project Details
Process Flow for CTA Button Modification in Digital Advertisement Image
Input: Digital Advertisement Image

Super Resolution

Output: Enhanced Image Quality (2X resolution)
Module: src/super_resolution
OCR

Output: Bounding Boxes of each text element
Module: src/ocr_module
Font Property Extraction

Output: List of text properties and bounding boxes
Module: src/font_prop_identification
Finetuned LLAMA3

Input: Word bounding boxes
Output: Extracted CTA text in JSON format (standard English)
Finetuning Notebook: notebooks/unsloth_finetuning
LLM Output Formatting

Output: CTA text in standard JSON format
Module: utils/json_extractor
Blob Creation Engine

Input: CTA text
Output: Single bounding box around the CTA text
Module: src/text_formatting/blob_engine
Inpainting Engine

Input: Bounding box around the CTA text
Output: Cleaned CTA button region
Module: src/inpainting
Generate Alternate CTA: LLAMA 3-Instruct

Input: Extracted CTA text
Output: Alternate CTA text
Module: src/text_generation
LLM Output Formatting

Output: Alternate CTA text in standard JSON format
Module: utils/json_extractor
Segmentation Engine

Input: Formatted alternate CTA text
Output: Final Output Image
