# Will-the-Customer-Accept-the-Coupon-
The goal of this project was to use visualizations and probability distributions to distinguish between customers who are likely to accept a driving coupon versus those who will not.

import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
import numpy as np

data = pd.read_csv('data/coupons.csv')
data.head()

#Investigating the dataset for missing or problematic data.
data.isnull().sum()

#Handling missing data
data.duplicated().sum()

#Duplicates: There are 74 duplicate rows. All duplicate rows are being kept as we are interested in every response.

#Missing data: With approximately 99% of the values for 'car' being blank it isn't really adding much value to the dataset,
#whereas the other columns missing values are missing so few values and assuming this will not have a significant impact overall.

# Clean column names to avoid hidden spaces or characters
data.columns = data.columns.str.strip()

# Drop the 'car' column safely
data = data.drop(columns=['car'], errors='ignore')

What proportion of the total observations chose to accept the coupon?
#proportion of the total observations chose to accept the coupon
data["Y"].value_counts()
print('Coupons accepted:',data['Y'].value_counts()[1]),
print('Total Coupons: ',data.shape[0])
accepted_vs_total = data['Y'].value_counts()[1]/data.shape[0]
print('Proportion of the total observations that chose to accept the coupon: ', accepted_vs_total)
#The coupon was accepted approximately 57% of the time.

5. Use a bar plot to visualize the 'coupon' column.
#A bar plot to visualize the 'coupon' column.
data['coupon'].value_counts().plot(kind='bar')
plt.title('Coupon Type Distribution')
plt.xlabel('Coupon Type') 
plt.xticks(rotation=45)  
plt.ylabel('Count')
plt.show()

6. Use a histogram to visualize the temperature column.
#A histogram to visualize the temperature column
sns.histplot(data['temperature'], kde=True, bins=30, color='skyblue')

plt.title('Temperature Distribution')
plt.xlabel('Temperature')
plt.ylabel('Count / Density')
plt.grid(False)
plt.show()

**Investigating the Bar Coupons**
Now, we will lead you through an exploration of just the bar related coupons.  
1. Create a new `DataFrame` that contains just the bar coupons.

data_bar_coupon = data[data['coupon']=='Bar']

2. What proportion of bar coupons were accepted?
#2.proportion of bar coupons that were accepted
print(data_bar_coupon['Y'].value_counts())
print(type(data_bar_coupon['Y'].value_counts()))

print("Bar coupons accepted:", data_bar_coupon['Y'].value_counts()[1])
print('Bar coupons total:', data_bar_coupon.shape[0])
"Proportion of accepted vs total"
bar_accepted_vs_total = data_bar_coupon['Y'].value_counts()[1]/data_bar_coupon.shape[0]
print('Proportion of the total observations that chose to accept the coupon: ', bar_accepted_vs_total)

print("Bar coupons accepted:", data_bar_coupon['Y'].value_counts()[1])
print('Bar coupons total:', data_bar_coupon.shape[0])
"Proportion of accepted vs total"
bar_accepted_vs_total = data_bar_coupon['Y'].value_counts()[1]/data_bar_coupon.shape[0]
print('Proportion of the total observations that chose to accept the coupon: ', bar_accepted_vs_total)

3. Compare the acceptance rate between those who went to a bar 3 or fewer times a month to those who went more.
#the acceptance rate between those who went to a bar 3 or fewer times a month to those who went more
data_bar_3_or_less = data_bar_coupon[data_bar_coupon['Bar'].isin(['never', 'less1', '1~3'])]
data_bar_more_than_3 = data_bar_coupon[data_bar_coupon['Bar'].isin(['4~8', 'gt8'])]
#Calculate acceptance rates for both groups\n",
acceptance_rate_3_or_less = data_bar_3_or_less['Y'].mean()
acceptance_rate_more_than_3 = data_bar_more_than_3['Y'].mean()
print('Acceptance Rate for 3 or fewer times a month:', acceptance_rate_3_or_less)
print('Acceptance Rate for more than 3 times a month:', acceptance_rate_more_than_3)
#The bar coupon was accepted approximately 37% of the time by people who went to the bar 3 or fewer times a month
#The bar coupon was accepted approximately 76% of the time by people who went to the bar more than 3 times a month.

4. Compare the acceptance rate between drivers who go to a bar more than once a month and are over the age of 25 to the all others.  Is there a difference?
data['age_numeric'] = data['age'].replace({'below21': 20, '50plus': 50}).astype(int)
data_bar_coupon = data[data['coupon']=='Bar']
# Filtering the dataset for drivers who go to a bar more than once a month and are over the age of 25
data_target_group = data_bar_coupon[(data_bar_coupon['Bar'].isin(['1~3', '4~8', 'gt8'])) & (data_bar_coupon['age_numeric'] > 25)]
data_other_group = data_bar_coupon[~((data_bar_coupon['Bar'].isin(['1~3', '4~8', 'gt8'])) & (data_bar_coupon['age_numeric'] > 25))]
# Calculate acceptance rates for both groups
acceptance_rate_target_group = data_target_group['Y'].mean()
acceptance_rate_other_group = data_other_group['Y'].mean()
print('Acceptance Rate for drivers who go to a bar more than once a month and are over the age of 25:', acceptance_rate_target_group)
print('Acceptance Rate for all others:', acceptance_rate_other_group)

#The bar coupon was accepted approximately 70% of the time by drivers who go to the bar more than once a month and are over the age of 25.
#The bar coupon was accepted approximately 34% of the time by all other drivers.
#Drivers who go to the bar more than once a month and are over 25 are more likely to accept the bar coupon.

5. Use the same process to compare the acceptance rate between drivers who go to bars more than once a month and had passengers that were not a kid and had occupations other than farming, fishing, or forestry.

filtered_data = data_bar_coupon[(data_bar_coupon['Bar'].isin(['1~3', '4~8', 'gt8'])) & (data_bar_coupon['passanger']!= 'Kid(s)') & (~data_bar_coupon['occupation'].isin(['Farming Fishing & Forestry']))]
# Calculate acceptance rates for the filtered group
acceptance_rate_filtered = filtered_data['Y'].mean()
# Calculate acceptance rates for all others
acceptance_rate_others = data[~data.index.isin(filtered_data.index)]['Y'].mean()
print('Acceptance Rate for specified group:', acceptance_rate_filtered)
print('Acceptance Rate for all others:', acceptance_rate_others)

#The bar coupon was accepted approximately 71% of the time by drivers who go to the bar more than once a month, had passengers that were not a kid, 
#and had occupations other than farming, fishing, or forestry.
#The bar coupon was accepted approximately 56% of the time by all other drivers.

6. Compare the acceptance rates between those drivers who:

- go to bars more than once a month, had passengers that were not a kid, and were not widowed *OR*
- go to bars more than once a month and are under the age of 30 *OR*
- go to cheap restaurants more than 4 times a month and income is less than 50K.

# Condition 1: Go to bars more than once a month, had passengers that were not a kid, and were not widowed
condition1 = (data_bar_coupon['Bar'].isin(['1~3', '4~8', 'gt8'])) & (data_bar_coupon['passanger'] != 'Kid(s)') & (data_bar_coupon['maritalStatus'] != 'Widowed')
# Condition 2: Go to bars more than once a month and are under the age of 30
condition2 = (data_bar_coupon['Bar'].isin(['1~3', '4~8', 'gt8'])) & (data_bar_coupon['age_numeric'] < 30)
# Condition 3: Go to cheap restaurants more than 4 times a month and income is less than 50K
condition3 = (data_bar_coupon['RestaurantLessThan20'].isin(['4~8', 'gt8'])) & (data_bar_coupon['income'].isin(['Less than $12500', '$12500 - $24999', '$25000 - $37499', '$37500 - $49999']))

dbc_filtered_c1 = data_bar_coupon[condition1]
dbc_filtered_c2 = data_bar_coupon[condition2]
dbc_filtered_c3 = data_bar_coupon[condition3]

# Calculate acceptance rates
dbc_c1_acceptance_rates = dbc_filtered_c1['Y'].mean()
dbc_c2_acceptance_rates = dbc_filtered_c2['Y'].mean()
dbc_c3_acceptance_rates = dbc_filtered_c3['Y'].mean()
print('Condition 1 - acceptance rate: ',dbc_c1_acceptance_rates)
print('Condition 2 - acceptance rate: ',dbc_c2_acceptance_rates)
print('Condition 3 - acceptance rate: ',dbc_c3_acceptance_rates)

#The bar coupon acceptance rate was approximately 71% for drivers who go to bars more than once a month, had passengers that were not a kid, and were not widowed.
#The bar coupon acceptance rate was approximately 72% for drivers who go to bars more than once a month and are under the age of 30.
#The bar coupon acceptance rate was approximately 45% for drivers who go to cheap restaurants more than 4 times a month and income is less than 50K"

#Based on the observations, we can hypothesize the following about drivers who accepted the bar coupons:
#High Frequency of Bar Visits:
#Drivers who frequently visit bars, especially more than 3 times a month, are significantly more likely to accept bar coupons. 
#This suggests a strong correlation between the frequency of bar visits and the likelihood of accepting related coupons, 
#possibly due to a higher value placed on such discounts by regular patrons.
#Age Factor: 
#Younger drivers, particularly those under the age of 30, show a higher acceptance rate for bar coupons. 
#This could indicate that younger demographics are more inclined towards social outings like bar visits 
#and are more receptive to discounts associated with such activities.
#Marital Status and Occupation:
#Drivers who are not widowed and have occupations outside of farming, fishing, or forestry are more likely to accept bar coupons.
#This might reflect lifestyle or social patterns where individuals in certain occupations or marital statuses 
#have more social engagements or value leisure activities differently.
#Economic and Dining Preferences
#Drivers with specific economic and dining preferences, such as those who frequent cheap restaurants and have an income of less than 50K, 
#have distinct acceptance rates. This could suggest that economic factors and personal dining habits influence the perceived value of bar coupons.
#General Acceptance Among Other Drivers:
#The relatively lower acceptance rate among "all other drivers" indicates that there are specific demographic 
#and behavioral traits that significantly influence the likelihood of accepting bar coupons. 
#This group likely includes drivers who do not frequently visit bars or do not fit into the specific demographic profiles outlined above.
#These hypotheses suggest that demographic factors (such as age and marital status), behavioral patterns (frequency of bar visits),
#economic status, and personal preferences (dining habits) play crucial roles in determining the likelihood of accepting bar coupons.


### Independent Investigation

Using the bar coupon example as motivation, you are to explore one of the other coupon groups and try to determine the characteristics of passengers who accept the coupons.  
#a.instances where Carry Out & Take away coupons were declined
data_declined_carryout = data[(data['coupon']=='Carry out & Take away') & (data['Y']==False)]
distr_by_age = data_declined_carryout['age_numeric'].value_counts(normalize=True) * 100
print('Distribution by Age:', distr_by_age)
fig, axes = plt.subplots(1, 2, figsize=(14, 5))

# Countplot for categorical age
sns.countplot(ax=axes[0], x='age', data=data_declined_carryout,
              palette='viridis',
              order=data_declined_carryout['age'].value_counts().index)
axes[0].set_title('Coupon Declines by Age Group')
axes[0].set_xlabel('Age Group')
axes[0].set_ylabel('Count')
axes[0].tick_params(axis='x', rotation=45)

# Histogram + KDE for numeric age
sns.histplot(ax=axes[1], data=data_declined_carryout,
             x='age_numeric', kde=True, bins=10, color='purple')
axes[1].set_title('Coupon Declines by Numeric Age')
axes[1].set_xlabel('Age (Numeric)')
axes[1].set_ylabel('Count / Density')

plt.tight_layout()
plt.show()

# b. declines by marital status\n",
distr_by_marital_status = data_declined_carryout['maritalStatus'].value_counts(normalize=True) * 100
print('Distribution by Marital Status:', distr_by_marital_status)
sns.countplot(x='maritalStatus', data=data_declined_carryout, palette='viridis', order = data_declined_carryout['maritalStatus'].value_counts().index, legend=False)
plt.title('Coupon Declines by Marital Status')

#c. declines by education
distr_by_education = data_declined_carryout['education'].value_counts(normalize=True) * 100
print('Distribution by Education:\\n', distr_by_education)
sns.countplot(x='education', data=data_declined_carryout, palette='viridis', order = data_declined_carryout['education'].value_counts().index, legend=False)
plt.title('Coupon Declines by Education')
plt.xticks(rotation=75)

#Observations
#Age: The age groups of 21 and 26 years old are the most likely to decline Carry Out & Take away coupons, 
#each constituting approximately 20% of the declines. 
#This suggests that younger adults in their early to mid-twenties are more inclined to decline these coupons.",
#Marital Status: The majority of declines come from individuals with a married partner (approximately 40%) and singles (approximately 37%).
#This indicates that marital status, particularly being married or single, plays a significant role in the likelihood of declining Carry Out 
#& Take away coupons.
#Education: Individuals with a Bachelor's degree are the most likely to decline these coupons, making up approximately 37% of the declines,
#followed by those with some college but no degree (approximately 30%).
#This suggests a higher tendency to decline among those with higher education levels
