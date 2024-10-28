# Debugging Yocto Recipes: Hands-On Video Tutorial

## Description
This repository serves as a hands-on guide for debugging recipes in the Yocto Project. It includes a comprehensive video tutorial and accompanying slides derived from the Bootlin tutorial for Yocto, aimed at helping developers effectively troubleshoot and resolve issues during the build process.

## Acknowledgment
The slides attached in this repository are sourced from the Bootlin tutorial for Yocto. We extend our gratitude to Bootlin for their valuable resources that facilitate learning and development in the Yocto ecosystem.

## Introduction
Debugging is a crucial aspect of software development, especially in complex build systems like Yocto. This repository aims to equip developers with practical knowledge and techniques for efficiently debugging recipes, improving build reliability and performance.

## Video Tutorial
[Watch the Video Tutorial](https://drive.google.com/file/d/13Su6BMfcX6kLUgUBNrtmoraawsbWD54Z/view?usp=sharing) 
This video provides a step-by-step walkthrough of various debugging techniques in Yocto.



## Debugging Techniques



---

## **Viewing Logs from Failed Tasks**

- When a task fails in Yocto, logs are essential for diagnosing the issue.
- You can find the log for a specific task in the file `${[WORKDIR](https://docs.yoctoproject.org/ref-manual/variables.html#term-WORKDIR)}/temp/log.do_`taskname`. For instance, the log for the [do_compile](https://docs.yoctoproject.org/ref-manual/tasks.html#ref-tasks-compile) task of the QEMU minimal image for the x86 machine (`qemux86`) might be located at `tmp/work/qemux86-poky-linux/core-image-minimal/1.0-r0/temp/log.do_compile`.

- To see the commands executed by [BitBake](https://docs.yoctoproject.org/ref-manual/terms.html#term-BitBake) that generated the log, refer to the corresponding `run.do_`taskname file in the same directory.

- Both `log.do_`taskname and `run.do_`taskname are symbolic links pointing to `log.do_`taskname`.`pid` and `log.run_`taskname`.`pid`, where `pid` is the process ID of the task during execution. These symlinks always refer to the most recent run, making it easier to access relevant logs.

--- 

![Screenshot from 2024-10-28 23-39-11](https://github.com/user-attachments/assets/84f2ce9d-da83-4772-8d3f-4b460aed8bd7)

Benefits of Using Logs 

- Help identify problems quickly.
- Provide insights into build processes.
- Improve recipe reliability by allowing developers to debug and optimize.



---

## Accessing Log Files for Debugging

To view the log files generated during the build process, follow these steps:

1. **Navigate to the Build Directory**:
   Change to the `build` directory where your Yocto project is located
   
2. **Enter the Temporary Directory**:
   Access the `tmp` directory, which contains all the build-related data
   
3. **Go to the Work Directory**:
   Enter the `work` directory to find the logs for specific recipes
  
4. **Select the Target Architecture**:
   Choose the appropriate architecture directory, for example, `qemux86_64-poky-linux`
  
5. **Access the Recipe Directory**:
   Navigate to the specific recipe directory you want to inspect. For example, for `base-files`

6. **Go to the Temp Directory**:
   Inside the recipe directory, enter the `temp` directory
   

7. **List Available Log Files**:
   List the contents of the `temp` directory to find relevant log files

### Important Log Files
- **`log.do_compile`**: Contains logs related to the compilation process of the recipe.
- **`run.do_compile`**: Shows the script that was executed during the compilation.
   
8. **View the Log Files**:
   Use `cat` or `less` to read the contents of the desired log files, such as `log.do_compile`:


## Example of Accessing Run/Log Files in My Bash

Here’s an example of how to access the run and log files using my own terminal:

![Screenshot from 2024-10-29 00-01-41](https://github.com/user-attachments/assets/f034228f-b22c-400c-a261-fafcfadd603a)


## Understanding the Log and Run Files

The following files are generated during the build process and provide valuable insights for debugging and optimizing your Yocto recipes:

### 1. **Log Files**

- **`log.do_cleanall`**: This log contains the output from the `do_cleanall` task, which is responsible for cleaning all output files for the recipe. It provides information on what was removed and any errors encountered during the cleaning process.

- **`log.do_cleanall.<pid>`**: Similar to `log.do_cleanall`, this file captures the log output for a specific run of the `do_cleanall` task, identified by the process ID (PID). It is useful for tracking specific executions.

- **`log.do_cleansstate`**: This log file records the output of the `do_cleansstate` task, which cleans the shared state cache for a recipe. It details the files that were cleaned and any issues that arose.

- **`log.do_cleansstate.<pid>`**: This is the log for a specific execution of the `do_cleansstate` task. It helps you understand the context of that particular run.

- **`log.task_order`**: This file outlines the order in which tasks are executed. It provides a comprehensive view of task dependencies and execution flow, helping to identify bottlenecks or conflicts.

### 2. **Run Files**

- **`run.do_cleanall`**: This is the script generated for the `do_cleanall` task. It contains the commands executed during the cleaning process. By inspecting this file, you can understand how the clean operation is performed.

- **`run.do_cleanall.<pid>`**: This file is similar to `run.do_cleanall`, but it corresponds to a specific run identified by the PID. It’s useful for debugging specific cleaning scenarios.

- **`run.do_cleansstate`**: This script outlines the commands executed during the `do_cleansstate` task. Reviewing this file can help you see what actions are taken to clean the shared state.

- **`run.do_cleansstate.<pid>`**: This file corresponds to a specific run of the `do_cleansstate` task, providing context for that execution.

### **Benefits of Using These Files**

1. **Debugging**: When tasks fail or produce unexpected results, these logs can help pinpoint the cause by providing detailed error messages and execution flow.

2. **Optimization**: Understanding the commands in the run files allows you to refine your recipes for better performance and reliability.

3. **Task Flow Analysis**: The `log.task_order` file helps visualize the dependencies and order of task execution, making it easier to optimize the build process.

4. **Error Tracking**: By reviewing specific log files for failed tasks, you can identify recurring issues and address them systematically.

### **Usage**

To utilize these files effectively:

- **Inspect Logs**: Start with the log files to get an overview of what happened during task execution.
- **Review Run Scripts**: Check the corresponding run scripts for detailed command sequences.
- **Trace Execution**: Use the `log.task_order` file to understand how tasks relate to each other and where improvements can be made.

By leveraging these resources, you can enhance your debugging capabilities and improve the overall reliability of your Yocto builds.

## Debugging Variable Assignment

Understanding how variables are assigned and modified in Yocto recipes is crucial for effective debugging and optimization. The following tools and techniques can help you inspect and troubleshoot variable assignments:

![image](https://github.com/user-attachments/assets/0b38ea8c-3b8d-47e3-b682-3d7b12c3dc08)


### Key Tools

1. **`bitbake -e <recipe-name>`**
   - **Purpose**: Outputs the complete environment for a specific recipe after parsing, showing all variable assignments.
   - **Usage**: Use this command to see how variables are set, inherited, and influenced by the various layers and configurations.
   - **Output**: The output includes all variable values, providing insights into any changes that may have occurred during parsing.

2. **`bitbake-getvar -r <variable-name>`**
   - **Purpose**: Retrieves the value of a specific variable as it applies to a recipe.
   - **Usage**: This command is particularly useful for inspecting variables without triggering task execution.
   - **Example**: Run `bitbake-getvar -r LDFLAGS <recipe-name>` to see how the `LDFLAGS` variable is set for the specified recipe.

### Common Variables to Inspect

- **`DEPENDS`**: Lists dependencies required for building the recipe. Understanding this variable can help resolve build order issues.
- **`LDFLAGS`**: Contains flags used during the linking phase. Inspecting this can provide insights into linking errors or optimization settings.
- **`CFLAGS`**: Compiler flags that affect the compilation process. Analyzing this can help diagnose compilation issues.

### Best Practices

- **Check Variable Precedence**: Variables can be overridden or modified at different layers. Use `bitbake -e` to trace variable changes throughout the build.
- **Use Logging**: Consider adding `bb.debug` logging statements in your recipe to print variable values during execution for better visibility.
- **Compare Builds**: If you suspect a variable is affecting the build outcome, compare logs and variable values from successful and failed builds.

### Example Workflow

1. **Identify the Problem**: If a recipe fails, start by identifying which variable might be causing the issue.
2. **Inspect Variables**: Use `bitbake -e <recipe-name>` to get a full list of variable assignments.
3. **Refine Assignments**: Modify your recipe or configuration files as necessary based on your findings.
4. **Test Changes**: Re-run the build to see if your adjustments resolve the issue.

By effectively debugging variable assignments, you can enhance the reliability and performance of your Yocto recipes, ensuring a smoother development process.

## Reference
For more information on debugging in Yocto, please refer to the [Yocto Project Debugging Manual](https://docs.yoctoproject.org/dev-manual/debugging.html).
