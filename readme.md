# Creative Mutation


## Setup Instructions

### Devcontainer Setup
1. Open the project folder in the devcontainer.
   - The devcontainer bootup will run all necessary installation scripts.

### llama3:70b Setup for Alternate CTA Generation
1. Start the llama server:
   ```bash
   ollama serve
   ```
   Pull the llama3:70b model:
   ```bash
   ollama pull llama3:70b
   ```
2. Google Cloud Vision Setup
  Ensure you have a project in Google Cloud with the Cloud Vision API enabled. Follow the setup instructions here.
  Authenticate with Google Cloud:
  ```bash
    gcloud auth application-default login
```
### Run the Workflow
Execute the main workflow notebook:
run 
``` bash
notebooks/cta_workflow.ipynb
```
### Finetuning the Model
run 
``` bash
notebooks/unsloth_finetuning.ipynb
```
##  Process Flow for CTA Button Modification in Digital Advertisement Image
![Project Flow Diagram](project_flow_diagram.png)
    - Input: Digital Advertisement Image
1. **Super Resolution**
    - Output: Improved Image Quality by 2X

2. **OCR**
    - Output: Bounding Boxes of each text
    - Module Used: `src/OCR Module`

3. **Font Prop Extraction**
    - Output: A boundBox List of all Text Props from Image
    - Module Used: `src/fontpropidentification`

4. **Finetuned LLAMA3**
    - Input: Word BoundBox List
    - Output: Extracted CTA Text JSON (as standard english)
    - Finetuning:`notebooks/unsloth finetuning`

5. **LLM Output Formatting**
    - Output: CTA Text in standard JSON Format
    - Module Used: `utils/jsonExtractor`

6. **Blob Creation Engine**
    - Input: CTA TEXT
    - Output: Single Bounding Box Around CTA Text
    - Module Used: `src/text_formatting/blob_engine` : 

7. **Inpainting Engine**
    - Input: Single Bounding Box Around CTA Text
    - Output: Cleaned CTA Button Region
    - Module Used: `src/in_painting` 

8. **Generate Alternate CTA: LLAMA 3-Instruct**
    - Input: Extracted CTA text
    - Output: ALT CTA Text
    - Module Used: `src/text_generation` 

9. **LLM Output Formatting**
    - Output: CTA Text in standard JSON Format
    - Module Used: `utils/jsonExtractor`

10. **Segmentation Engine**
    - Input: Formatted ALT CTA Text
    - Output: <i><u>FINAL OUTPUT IMAGE</i></u>
