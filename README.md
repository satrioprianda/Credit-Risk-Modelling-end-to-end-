# Credit-Risk-Modelling-end-to-end-
build credit risk modelling from feature engineering &amp; Model evaluation. evaluate the model and predictors using several metrics which usually used on business case.



Objective : prepare, process data , build and evaluate the model 

Content Overview
1. Raw data overview
2. Feature engineering 
3. Final modelling & result 


**1. Raw Data overview** 
Overall there is 22 columns which contains information provided on the raw data which have all 
information to build credit risk model . the information shows application information on certain 
period with index as identifier and de_date_joined as date_application information. The data assumed 
only on approved population which no credit status app information. 
• Index ( unique identifier ) 
• Flag_bad → dpd target ( 1,0 client’s default inf)
• De_date_joined → date application of client 
• Potential predictors/feature
o Client information 
o Apps list 
o Call log & FB information 


**2. Feature engineering**


On the data raw provided there are several predictor domain that can be potential predictors.
- Demography 
o Age, cnt_children , declared_salary, education, employment_type, housing_type, etc 
- Application list ( information of mobile apps installed in clients ) 
o Installed shopping application 
o Installed fintech or competitor apps on the client’s mobile apps prior to application date
o Installed gambling apps information 
o Other category apps 
o Limited to only flag installed/ no-installed, but Have no further information of usage or 
latest update information
- Call log list 
o Calling duration on night, day etc
- Facebook information 
o Latest update
o Count friends 
o etc
This item's classification is Internal. It was created by and is in property of the Home Credit. Do not distribute outside of the organization.
using several aggregation and feature engineering calculation, ratio , summation, average, date_diff , 
etc to there are around → 800++ Raw Predictors




**3. Final modelling result**


Monthly Data Split for Modelling
Train, valid, oot ( oot is period where not altered by modelling process)
Used mainly for stability checking 
Development Period : January 2018 – May 2018 → 5 months observation 
OOT period : June 2018 – December 2018 →7 months observation but on monthly basis 
less contract available ( potential data limitation , can potentially leads to instability performance 
within this period )

<div align="center">
  <img width="590" height="366" alt="image" src="https://github.com/user-attachments/assets/9eedf665-c7fc-47a7-914f-e9f130503ab9" />
</div>

**Feature Selection**


The idea of feature selection is to narrow down and select sub-final predictor based on its 
predictiveness toward target. several method are commonly used in credit risk modelling in general 
such as :
- Correlation 
- Feature importance
- Shap importance
On this specific modelling, feature importance using LGBM will be used . this method doesn’trequire 
the predictor to be encoded or transformed into more “proper” form. LGBM can directly consume 
predictor with also including null values. 
This item's classification is Internal. It was created by and is in property of the Home Credit. Do not distribute outside of the organization.
Feature importance
Quickly build lgbm model with all raw/transformed predictor to Calculate each predictors’ contribution 
toward final discriminatory power of model to predict given target. on this step the idea is to exclude 
all predictor that has 0 importance value → not contributive toward final model performance

<div align="center">
<img width="639" height="409" alt="image" src="https://github.com/user-attachments/assets/cd102767-e976-4c15-ac07-4af2d4fdc562" />
</div>

Univariate Gini by predictor (%)
Quickly check predictor univariate power. Checking which level will be included in sub final 
predictors. Univariate Gini show how each predictor singulary have discriminatory power over DPD Target on the modelling ( flag Bad )


<div align="center">
<img width="752" height="511" alt="image" src="https://github.com/user-attachments/assets/089e4187-c659-4b0d-bc97-4ab99bf25336" />
</div>

**Final feature**
Using combination rules of correlation < 0.4, no minus contribution to final model ( make sure the 
predictor Marginal contribution is positive when added ) , selected final feature of model

<div align="center">
<img width="674" height="471" alt="image" src="https://github.com/user-attachments/assets/146eadd9-7f40-4bcc-ad19-94dd6c1d1d4a" />
</div>

**Shap Importance**
- show risk relationship between value predictor with the target. 
- the more separation , shows the better the predictor to separate between good n bad clients. usually translated and allign with Univariate Gini of the predictor 

<div align="center">
<img width="737" height="502" alt="image" src="https://github.com/user-attachments/assets/cb7dc71b-c492-418e-a68a-4467fc974cec" />
</div>

**Predictor Grouping (WOE and IV )**

To better help understand the WOE transformation and the binning each predictor, additionally gain 
its relationship with the Risk 

example 2 feature of WOE & IV 

<img width="755" height="397" alt="image" src="https://github.com/user-attachments/assets/b2786e76-d2b0-4d84-89f5-9f276496f715" />




4. Model Building
Final model using Logistic regression to avoid over-fitting on the development and production period 
especially , aiming more on stable performance , less complex, operationally excellence and easier 
for business interpretation




**Marginal Contribution**

the objective is to check which predictors contribute the most in term of performance on the final 
model Gini, which later is shown by the marginal contribution. 

<img width="519" height="192" alt="image" src="https://github.com/user-attachments/assets/07cd466b-b60b-489d-b5c4-87a078979695" />



Correlation
- Checking the correlation is to ensure no final predictor have high correlation between another, it 
wouldn’t make sense to maintain 2 predictors in production which highly correlated each other in 
term of mathematics relationship .
<img width="586" height="499" alt="image" src="https://github.com/user-attachments/assets/5474b145-e068-450e-9889-a233ef6f4fed" />


**5. Model performance**

By sample set
Overall the performance of the final model is quite stable, no suspected over-fit model appeared on 
the performance , especially on the OOT. Gini on the train around 48%, valid 47% while on the OOT 
sample set ( more recent period ) around 47% gini which in general still performs quite good and 
stable. 

based on picture below, the model doesn't show overfit. meaning diff relative% between train and other validation data set <10% as practical rule of thumb overfit on Industry.

<img width="589" height="351" alt="image" src="https://github.com/user-attachments/assets/195bcca1-5f5e-4d5f-a092-3c92a1d4215d" />



**LIFT & Calibration**

To shows how well model performed compared to random guessing ( lift ) while the calibration
shows how well the predicted probabilities match the actual outcomes. In other words, it shows 
whether the predicted probabilities are well-calibrated, meaning they correspond closely to the true 
likelihood of the event

<img width="657" height="372" alt="image" src="https://github.com/user-attachments/assets/20e99ac5-cf24-4cd7-9f3e-abc7f7123a40" />


**Final Score Monotonicity**
Show the bad stability index, or score-risk linear relationship stability by sample set. If the linear 
relationship and the monotonicity is stable throughout different sample set or period, the model will 
have stable sorting capability to distinct good and bad incoming application. Which potentially leads 
to better performance and better approved population. 


<img width="569" height="352" alt="image" src="https://github.com/user-attachments/assets/d0a5bbdc-b890-4d88-93a0-31b7d9d7a737" />




