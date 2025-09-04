## COURSE: "Fine-Tuning and Validating an AI Perception Model" > "Lecture: Training a Model With Synthetic Data": 
https://learn.nvidia.com/courses/course?course_id=course-v1:DLI+S-OV-30+V1&unit=block-v1:DLI+S-OV-30+V1+type@vertical+block@aced7cf26b974581baf48fae53b70341

0 - Make sure you have completed all the previous steps from the course, including the one to generate synthetic data: from Lecture “Generating a Synthetic Dataset Using Replicator”
. Clone repo: https://github.com/NVIDIA-AI-IOT/synthetic_data_generation_training_workflow.git
. Configure generate_data.sh
. Run generate_data.sh
. Full description in the file: Nvidia Isaac SIM COURSE_Generating a Synthetic Dataset Using Replicator.txt

1- Open Ubuntu CLI (Linux environment)

2- create a separate Conda environment that users python version 3.10: "conda create -n tao-py310 python=3.10 
 If this environment has already been previously created, skip this step.

3- Activate python 3.10 conda env: "conda activate tao-py310"

4- Connect to Nvidia's docker container:
. Open docker desktop application and click on the play button on the container in the container list
. in the ubuntu cli run "docker login nvcr.io"
. login to the Nvidia container (if already logged user and password were already saved, if not get user (API key) from https://org.ngc.nvidia.com/setup/api-keys and rotate password to get a new password)
. run "docker ps -a" to check active containers.

5- navigate, in Ubuntu CLI, to folder where the GitHub project for synthetic data generation was cloned (from project https://learn.nvidia.com/courses/course?course_id=course-v1:DLI+S-OV-30+V1&unit=block-v1:DLI+S-OV-30+V1+type@vertical+block@7fecaf9f66204c0ea35402fca5ae1b25: "Generating a Synthetic Dataset Using Replicator > Activity: Understanding Basics of the SDG Script": 
"cd [MY LOCAL PATH]/GitHub/synthetic_data_generation_training_workflow/local" (replace "[MY LOCAL PATH]")
. go to the folder where you saved the repo from the previous step “Generating a Synthetic Dataset Using Replicator” and run local_train.ipynb from there. 
. DO NOT download the project from the link provided here: “Fine-Tuning and Validating an AI Perception Model > Lecture: Training a Model With Synthetic Data” > “Optional: Training Your Own Model". The notebook there has a slightly different code that will throw an error. (this wasn't in the Nvidia original documentation)

6- Open the notebook in this folder from Ubuntu CLI: "jupyter notebook local_train.ipynb --allow-root"
- Copy the URL provided in my web browser, click on the notebook to open

### INSIDE THE NOTEBOOK

1- Do not uncomment or replace ```# os.environ["LOCAL_PROJECT_DIR"] = "<LOCAL_PATH_OF_CLONED_REPO>"``` this code is not used anywhere (this wasn't in the Nvidia original documentation)..

2- On item 5 "Run TAO Training" add this line of code after "setting up env variables": ```!rm -rf $LOCAL_PROJECT_DIR/local/training/tao/detectnet_v2/resnet18_palletjack/*```
This is if you are going to run the model more than one. Every run, it creates log files with checkpoints. If you run the model again it will pick up from where it stopped training (checkpoints). If you update the spec file it will not use the new specs unless you delete the log files. This line of code is to Delete old log files from previous run (this wasn't in the Nvidia original documentation).

3- Change the original spec file found here: "LOCAL_PROJECT_DIR/local/training/tao/specs/training/resnet18_distractors.txt"
. Change:
```
  learning_rate {
    soft_start_annealing_schedule {
      min_learning_rate: 5e-06
      max_learning_rate: 5e-04
```
by 
```
  learning_rate {
    soft_start_annealing_schedule {
      min_learning_rate: 5e-06
      max_learning_rate: 4.75e-04
```
This slightly reduces the learning rate. If you try to run with the values suggested by the Nvidia notebook step 5 will show "Execution status: PASS".
This is because there is a post training assert (check) that asserts that the execution time of the soft-start + annealing has to be <= 1 (100%). If it is higher there might be an overlap between these two training stages which might impact in the training quality. 

Save the file.

4- Fix step 7 "Visualize Model Performance on Real World Data"
. the original notebook tries to fetch results from the wrong folder using wrong file names. Replace the original code by this one:
```
from IPython.display import Image 
import glob

# results_dir = os.path.join(os.environ["LOCAL_PROJECT_DIR"], "local/training/tao/detectnet_v2/resnet18_palletjack/test_loco/images_annotated")
# the original code tries to fetch inference result files from "local/training/tao/detectnet_v2/resnet18_palletjack/test_loco/images_annotated"
# but the inference code saves the results files in " -o /workspace/tao-experiments/local/training/tao/detectnet_v2/resnet18_palletjack/5k_model_synthetic \"

# 1. Point results_dir to the actual inference output
results_dir = os.path.join(
    os.environ["LOCAL_PROJECT_DIR"],
    "local/training/tao/detectnet_v2/resnet18_palletjack/5k_model_synthetic/images_annotated"
)

# pil_img = Image(filename=os.path.join(os.getenv("LOCAL_PROJECT_DIR"), 'detectnet_v2/july_resnet18_trials/new_pellet_distractors_10k/test_loco/images_annotated/1564562568.298206.jpg'))

# 2. Fix pil_img to use the same results_dir
# pil_img = Image(filename=os.path.join(results_dir, "1564562568.298206.jpg"))
# pil_img is not being used anywhere in the code

# image_names = ["1564562568.298206.jpg", "1564562628.517229.jpg", "1564562843.0618184.jpg", "593768,3659.jpg", "516447400,977.jpg"] 
# These are not the actual file names that got created   

# 2. Discover all image files (jpg, jpeg, png)
image_names = sorted(
    [os.path.basename(p) for p in glob.glob(os.path.join(results_dir, "*.jpg"))]
    + [os.path.basename(p) for p in glob.glob(os.path.join(results_dir, "*.jpeg"))]
    + [os.path.basename(p) for p in glob.glob(os.path.join(results_dir, "*.png"))]
)

images = [Image(filename = os.path.join(results_dir, image_name)) for image_name in image_names]

print(f"Found {len(image_names)} images")

display(*images)
```
5- run all cells in the notebook

6- Observe results from step 6. Evaluate Trained Model. Example: 
```
Validation cost: 0.002357
Mean average_precision (in %): 82.2011

class name      average precision (in %)
------------  --------------------------
palletjack                       82.2011
```
