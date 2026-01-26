# Rubric for Final Project

Your final report will follow the format of a standard scientific report, and will be written either in **markdown** format or as a **jupyter notebook**.  The final report itself will focus on the narrative, but all existing analyses must be available in a set of jupyter notebooks organized in the `work` folder, and named so that the purpose of each notebook is clear - e.g., `data_wrangling.ipynb` or `preprocessing.ipynb`. Your final narrative should include an index that tells me exactly what each supporting jupyter notebook does - do not just throw a bunch of notebooks in the folder and expect me to figure out their purpose. 

Please do **not** submit data directly, but instead provide links where the data can be retrieved (feel free to share a google drive with the data), or (if the data cannot be readily shared) where the data is described.  

**Only your final report and any supporting images will be in this folder**. Supporting notebooks will live in the `work` folder. The report will be named `submission.md` or `submission.ipynb.`  Failure to follow the above instructions will result in an automatic 2 point deduction without exception. Your report will have the following sections.

### Title

A nice short title for your project, helping me to understand roughly what it is you are trying to do.

### Team

This section should include the full names of each team member, **along with their github ids**.

### Introduction

This section will introduce your project.  It will tell me what are you trying to do, and who your primary stakeholder is. It will also explain the stakeholder need(s) that you are trying to address, and explain why this is a significant need. You will also briefly summarize your solution here, and explain how it addresses the expressed need.  If the project does not fully address the stated need, then you should explain how what you have done can be understood as an incremental step in this direction. This section should be no more than a few paragraphs in length.  

Note that I will evaluate your project in part according to whether ot not you can establish a connection between the work you have done and the need identified. Hence if you state that your stakeholder is a medical insurance provider who is trying to minimize costs without reducing levels of care, and your project predicts medical costs for patients with various symptom profiles, you will need to provide convincing arguments that connect the identified need with the ML solution you've come up with.

### Literature Review

This section will provide additional detail about your problem; feel free to copy some of this from your proposal. I would like you to clearly establish your stakeholder need(s), and then tell me why you chose the methods you chose based on prior work and the nature of your problem. If no one has worked on your problem (unlikely) you can say that, but should then talk about related problems that apply similar methods.

### Data and Methods

This section will introduce your data and specify your modeling approach.

#### Data

This will describe your dataset, including where you got it from and how you know it's good data.  It will include some discussion of your data - e.g., the number of rows and columns, types of features, balance and across classes.  You will include visualizations (2-5) that will help the reader to understand your data. Your supporting jupyter notebooks will contain additional analysis from your EDA.

#### Methods

Describe your methods. This will likely be one of the longer sections in the document - this is where you demonstrate to me how much work you did, even if you don't have great results at the end. Explain your modeling approach, and be sure to specify what your target variable/s is/are. Explain any preprocessing or data transformations that were necessary. Then explain the methods that you used and how you applied them.  If you tried multiple methods that didn't work, please explain what you did and why they didn't work. If your methods are novel or were not discussed in class, please explain these to provide me with and understanding of the method and confidence that you've applied the methods correctly. Do **not** include final results here; this section is intended to give the reader an overview of the steps taken. If you tried some things that didn't work, you can explain that here, but please be clear about what your final "best" approach was.

#### Supporting files

This section is not necessary if the project is contained in a single jupyter notebook, but most projects will have at least a handful of supporting files that illustrate all of the wonderful work you've done, even if it didn't make it into the final project.


### Results

Share the final outcomes of your work here.  Make sure to include appropriate metrics (note, accuracy score is usually not enough; you'll want to use f1-scores and confusion matrices.  If using regression, make sure to explain your results in terms of the original units - e.g., dollars, tons, etc. If a regression, you might consider evaluating performance across a distribution of scores by binning your data and reporting bin-specific RMSE / MAE / MPE). Explain how these metrics were generated (in most cases, you will be using n-fold cross validation; if not, explain why).  If you examined feature importance or clustered your data, provide your results here.  This section should include visualizations and or tables to illustrate your results, but please only include results necessary to support your claims. Any additional results can be left in the jupyter notebooks, and you should indicate these results may be found there. Additionally, if you created a prototype, please include a screenshot of the prototype; if the prototype is accessible either via a local server or the web, make sure I know how to run the code but do *not* include that here.  Instead, include an appendix, a footnote or a jupyter notebook with instructions.


### Discussion

Use this section to reflect on your results and tell me to what degree you achieved your goals.  Please be frank about this!  If you don't think you did, the simply tell me that and explain why.  Critically, you will reflect on your stakeholder needs, and explain how your work addresses those stakeholder needs.  If there is work to be done to address your stakeholder needs, explain this work.

### Limitations

Please explain how your work is limited - that is, how could it be better?  What would you have liked to have done but were not able to?  If you address some, but not all, of your envisioned stakeholder needs, explain what those are. How could you make your work more robust?  Are there reasons to be concerned about the quality of your results or performance scores?  Be critical of your own work, so that I don't have to.

### Future work

This should be self-explanatory - what are you next steps?  Where would you like to go from here?

---

I am not going to provide a detailed scoring breakdown here, as every project should have different emphasis and it's not reasonable to provide a granular breakdown.  I will provide written feedback to justify your final grade. 

