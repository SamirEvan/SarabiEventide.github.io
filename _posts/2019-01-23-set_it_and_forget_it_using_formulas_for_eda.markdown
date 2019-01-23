---
layout: post
title:      "Set it and Forget it: Using Formulas for EDA"
date:       2019-01-23 14:20:26 -0500
permalink:  set_it_and_forget_it_using_formulas_for_eda
---


Optimizing work flow is extremely important when working on data alaysis projects. 


It often takes several passes to clean and explore the data, so anything that can shave a few seconds off a step in the process can lead to minutes (or even hours) of saved time in the long run. Fortunately, there are a number of libraries that help reduce the amount of time we spend typing out regression formulas, but occasionally, you'll find yourself repeating the same/similar lines of code as you run through your tests. 


During my [first exploratory data analysis project,](https://github.com/SarabiEventide/dsc-1-final-project-online-ds-sp-000) I realized I'd be comparing a lot of scatter plots and cleaning outliers. I also knew I'd have to repeatedly test my regression models. Writing the functions in the beginning (or rather, copying them from previous exercises and adjusting them as necessary) allowed me to move swiftly through my analysis. 


### Scatter Plots 

```

def multi_scatter(column_names = None, target = None):
    fig, axs = plt.subplots(1, 4, sharey=True, figsize=(18, 6))
    for idx, feature in enumerate(column_names):
        df.plot(kind='scatter', x=feature, y=target, ax=axs[idx], label=feature, alpha = 0.3)
    plt.legend()
		
```
		

It's much faster to view several scatter plots at once than it is to plot each column's data individually. That said, large matrices of scatterplots are hard to read. I found that 4 plots fit neatly on one line. The `column_names` parameter allows you to pass in the column's you're interested in as arguments. The `target` is the variable you'd like to compare the others against. `figsize` and `alpha` can be adjusted as per preference, but I've found that (18,6) and "0.3" (respectively) produce the cleanest results. 


### Eliminating outliers

```

def drop_max(column_name = None):
    max_val = df[column_name].idxmax()
    df[column_name] = df[column_name].drop([max_val])
    return df.boxplot([column_name])
		
```
	
	
There are several ways to deal with outliers, but if you're as visual as I am, box plots make it easy to spot them. This function takes the column name as an argument, finds the highest value (which you've already determined to be an outlier at this point) and drops it. Then it returns another box plot so you can see the distribution of the data again and decide whether to repeat the process. 


### Heatmaps

I also wrote a function for heatmaps, but because I'll be discussing it in greater detail in a later post, I'll skip over it for now. Here it is for reference, though. 

```

def correlation_heatmap(df): 
    _, ax = plt.subplots(figsize = (15, 10))
    colormap= sns.diverging_palette(220, 10, as_cmap = True)
    sns.heatmap(df.corr(), annot=True, cmap = colormap)
		
```


### Ordinary Least Squares Regression Function 

```

def quick_ols(DataFrame = None, target = None):   # formula to run ordinary least squares
    col_names = DataFrame.describe().columns.drop([target])
    results = [['ind_var', 'r_squared', 'intercept', 'slope', 'p-value' ]]
    for idx, val in enumerate(col_names):
        print ("Housing: {}~".format(target) + val)
        print ("------------------------------")

        f = '{}~'.format(target) + val
        model = smf.ols(formula=f, data=DataFrame).fit()
        X_new = pd.DataFrame({val: [DataFrame[val].min(), DataFrame[val].max()]});
        preds = model.predict(X_new)
        results.append([val, model.rsquared, model.params[0], model.params[1], model.pvalues[1] ])
        print(results[idx+1])
				
``` 
		
This is perhaps the most useful function I wrote. It returns a neatly formated table similar to the one you get when you use statsmodels' multilinear regression methods. The difference here is that `quick_ols` allows you to quickly iterate through several features to determine which ones best predict your target.
		
		
You start by passing the dataframe and target column into the function. The second line reads the dataframe and create a list of features (`col_names)`. Next, the function runs through the features list and prints the name of the feature, the r-squared value, the intercept, the slope and the p-value.  


The second half of the function is the "nuts and bolts"  section. It creates the formula for ols regression, creates a model, fits the model and returns predictions. Rather than remembering all of the syntax for that, this function allows me to focus on the two most important pieces of information: the features and the targets. With this function, you don't even need to remember the name of the features because they'll be printed in the results. 		


### Are these functions really necessary?

Strictly speaking, no, writing functions are not always necessary. I could just as easily type out the syntax for scatterplots each time I want to make them... OR I could type it once then type a single command the next time I need it. Defining functions gives you the ability to customize built in functions. Customization is important because both computers AND humans need to be able to read your code. It makes no difference to the computer whether you print your results as successive arrays, but human eyes need things to be properly arranged. I added colors, dividers and line breaks for precisely this purpose. 


Pro data scientists are probably already familiar with the concept of writing functions to improve work flow, but if you're relatively new to data science it's definitely something worth exploring. 


		
