# icse2019

This research artifact contains the Python code used for the paper Going
<i>Farther Together: The Impact of Social Capital on Sustained
Participation in Open Source.</i>
The artifact can be used to select open source contributors, collect
their GitHub projects, gather data such as contributors’ years of
experience on GitHub, and projects’ age and size. 
It can also be used to
calculate social capital measures, including team familiarity, recurring
cohesion, and heterogeneity of programming language expertise. 
The final
output is a csv file, each row of which is a data point used to evaluate
the impact of social capital on sustained participation in open source.
Each data point consists of information of one contributor’s one project
during one three-month time window and all the social capital measures.
The code was
implemented in Python 2 and tested on a Linux machine. To run our code,
one needs to install the Python pymysql, sqlalchemy, numpy, sklearn, and
pandas libraries. One also needs to have a MySQL database for GHTorrent
data.

### Required dependencies:

pymysql

pickle

sqlalchemy

pandas

scipy

### Tables in the MySQL database:
(created using the MySQL query provided in
`MySQL_queries/ght_namsor_s`)

users

commits

ght_namsor_s 


### Procedure of running the code: 
1. Use `MySQL_queries/filter_valid_users` to find valid users. 

2. Run `sample_user.py` to get a balanced sample of equal number of male and female contributors. The result is saved in `data/uid.list`.
In order to obtain a sample with equal number of men and women,
`sample_user.py` calls our gender classifier to determine users'
genders.
The code for gender classifier are stored in the `gender/` folder. 
Details about these files are in the following section.

3. Run `setup.py`, which reads files `dict/alias_map_b.dict`,
`dict/reverse_alias_map_b.dict`, and `data/uid.list`, and generates files
`data/pid.list`, `data/all_contributors.list`, `dict/contr_projs.dict`,
`data/all_projs.list`, and `dict/proj_contrs_count.dict`.

4. Run `get_user_info.py`, `get_proj_info.py`, and `get_user_proj_info.py`. They write to `data/results_users.csv`, `data/results_proj.csv`, and `data/results_user_proj.csv` repectively.

5. Run `merge_result.py` to combine these tables. The result will be saved in `data/proj_user_proj.csv`, which will be used for data analysis.

### To determine gender:

Our gender classifier uses names' n-grams as well as results from other existing gender
classifiers, such as <a href="http://namsor.com">namsor</a> and 
<a href="https://github.com/tue-mdse/genderComputer">genderComputer</a>
as features.
One of the gender information sources we use was from Google+ by
searching the names and calculates the distribution of genders in the
serach results.
However, that particular Google+ API was deprecated.
Therefore, we had to remove it from our model. 

In the `gender/` folder are two Python files that demostrate how our
gender classifer works.
First, `get_feature.py` reads users' information from the MySQL
`ght_namsor_s` table, which contains users' information from GHTorrent
and origin and gender information obtained from <a
href="http://namsor.com">namsor</a>. 
Then it gets classification results from other gender classifiers. 
In the current file, it calls <a
href="https://github.com/tue-mdse/genderComputer">genderComputer</a>.
To get a better result from genderComputer, we need to know the user's
country. 
Namsor provided inference on one's origin based on their names. 
Therefore, we used this information when we call genderComputer. 

There are other gender classifiers one can use, e.g., genderio. 
To use them, simply make the result a new feature in the model. 

In `determine_gender.py`, our classifier divides the name into n-grams
and use them as additional features.
The result will be written to `data/gender.csv`, which will later be
used in `sample_user.py` for balance sampling.
