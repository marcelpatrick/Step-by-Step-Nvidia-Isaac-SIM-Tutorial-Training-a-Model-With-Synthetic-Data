COURSE: "Fine-Tuning and Validating an AI Perception Model" > "Lecture: Training a Model With Synthetic Data"

https://learn.nvidia.com/courses/course?course_id=course-v1:DLI+S-OV-30+V1&unit=block-v1:DLI+S-OV-30+V1+type@vertical+block@aced7cf26b974581baf48fae53b70341


0 - Make sure you have completed all the previous steps to generate synthetic data: 
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
"cd /mnt/c/Users/myali/Documents/GitHub/synthetic_data_generation_training_workflow/local"

6- Open the notebook in this folder from Ubuntu CLI: "jupyter notebook local_train.ipynb --allow-root"
- Copy the URL provided in my web browser, click on the notebook to open

7- inside the notebook, replace "# os.environ["LOCAL_PROJECT_DIR"] = "<LOCAL_PATH_OF_CLONED_REPO>"  by the actual path where I saved my cloned project used during the synthetic data generation step. "os.environ["LOCAL_PROJECT_DIR"] =  "/mnt/c/Users/myali/Documents/GitHub/synthetic_data_generation_training_workflow"

8- run all cells in the notebook
