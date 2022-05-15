# Part 1 (Three Questions)


## 1. Write a regex to extract all the numbers with orange color background from the below text in italics.*


   * {"orders":[{"id":**1**},{"id":**2**},{"id":**3**},{"id":**4**},{"id":**5**},{"id":**6**},{"id":**7**},{"id":**8**},{"id":**9**},{"id":**10**},{"id":**11**},{"id":**648**},{"id":**649**},{"id":**650**},{"id":**651**},{"id":**652**},{"id":**653**}],"errors":[{"code":**3**,"message":"[PHP Warning #2] count(): Parameter must be an array or an object that implements Countable (153)"}]}


**The following is the python code to extract indicated numbers as a list.


```python
import re
string="""{"orders":[{"id":1},{"id":2},{"id":3},{"id":4},{"id":5},{"id":6},
{"id":7},{"id":8},{"id":9},{"id":10},{"id":11},{"id":648},{"id":649},{"id":650},
{"id":651},{"id":652},{"id":653}],"errors":[{"code":3,"message":
"[PHP Warning #2] count(): Parameter must be an array or an object that 
implements Countable (153)"}]}"""
pattern=":(\d+)"
extracted_list=re.findall(pattern,string)
print(extracted_list)
```

    ['1', '2', '3', '4', '5', '6', '7', '8', '9', '10', '11', '648', '649', '650', '651', '652', '653', '3']
    

## 2. Here’s the list of reviews of Chrome apps - scraped from Playstore. (https://drive.google.com/file/d/1SuiFw4MYxOBlqsRgyXLfch28-gzx8bX6/view?usp=sharing)

**Problem statement** - There are times when a user writes Good, Nice App or any other positive text, in the review and gives 1-star rating. Your goal is to identify the reviews where the semantics of review text does not match rating. 

Your goal is to identify such ratings where review text is good, but rating is negative- so that the support team can point this to users. 

**Deploy it using** - Flask/Streamlit etc and share the live link. 

**Important**
*In this data app* - the user will upload a csv and you would be required to display the reviews where the content doesn’t match ratings.  This csv will be in the same format as the [DataSet Link](https://drive.google.com/file/d/1SuiFw4MYxOBlqsRgyXLfch28-gzx8bX6/view?usp=sharing)


**Bonus Points **- If you deploy the app with Authentication.

**Solution**

The following is the python code to deploy the required problem using streamlit with authentication.

Two users are created whose details are follows:

Username: madhav
password: 123456

Username: nlabs
password: 456789

The link for the same is - https://share.streamlit.io/madhav-reddy0903/review-mismatch/main/final.py

The github repository for the app is - https://github.com/madhav-reddy0903/review-mismatch




```python
import pandas as pd
import streamlit as st
import nltk
nltk.downloader.download('vader_lexicon')
from nltk.sentiment.vader import SentimentIntensityAnalyzer
import streamlit_authenticator as stauth

def identifier(df):
    sentiments = SentimentIntensityAnalyzer()
    data=df[['ID', 'Text', 'Star', 'User Name']]
    data=data[data['Text'].notna()]
    data["Positive"] = [sentiments.polarity_scores(i)["pos"] for i in data["Text"]]
    red_data=data.loc[(data["Positive"]>0.5) & (data["Star"]<3)]
    output=red_data[['Text', 'Star', 'User Name']]
    return output
   
names = ['Madhav Reddy','Next Labs']
usernames = ['madhav','nlabs']
passwords = ['123456','456789']
hashed_passwords = stauth.Hasher(passwords).generate()
authenticator = stauth.Authenticate(names,usernames,hashed_passwords,
'some_cookie_name','some_signature_key',cookie_expiry_days=30)
name, authentication_status, username = authenticator.login('Login','main')
if authentication_status:
    authenticator.logout('Logout', 'main')
    st.write('Welcome *%s*' % (name))
    html_temp = """
    <div>
    <h1 style ="color:white;text-align:center;"> Incorrect ratings identifier </h1>
    </div>
    """
    st.markdown(html_temp, unsafe_allow_html = True)
    uploaded_file = st.file_uploader("Upload the csv file", type='csv')
    if uploaded_file is not None:
        df1=pd.read_csv(uploaded_file)
        st.write("The following is the list of users whose reviews mismatch with the ratings")
        st.dataframe(identifier(df1))
elif authentication_status == False:
    st.error('Username/password is incorrect')
elif authentication_status == None:
    st.warning('Please enter your username and password')
```

The login screen looks like this:
![login%20screen.png](attachment:login%20screen.png)

Once the required details are entered, it takes to the following page where the user will be given provision to upload the csv file.
![upload%20screen.png](attachment:upload%20screen.png)

Finally, once the user uploads the csv file, the app will display the list of user names along with ID, the review text and the rating where the reviews are good but the rating given is low (1 or 2 stars). The screen looks like this:
![result.png](attachment:result.png)

## 3.	Ranking Data - Understanding the co-relation between keyword rankings with description or any other attribute. Here’s the dataset. 
https://drive.google.com/file/d/1yuDyU7EjJ8Nai83FDdIF2w4inm17NzBF/view?usp=sharing

**Suggested questions:**

1.	Is there any co-relation between short description, long description and ranking? Does the placement of keyword (for example - using a keyword in the first 10 words - have any co-relation with the ranking)?
2.	Does APP ID (Also known as package name) play any role in ranking?  
3.	Any other pattern or good questions that you can think of and answer?




```python
import pandas as pd
import streamlit as st
import nltk
import os
os.chdir(r'C:\Users\madha\My Drive\Desktop\Python\Nextlabs')
from nltk.corpus import stopwords 
from nltk.tokenize import word_tokenize 
df=pd.read_csv(r'Ranking data\browser_rankings_data.csv')
def ranks(df1, column):
    temp={column:[], 'Highest rank':[], 'Lowest rank':[]}
    for des in df1[column].unique():
        red_data=df1[df1[column]== des]
        temp[column].append(des)
        temp['Highest rank'].append(red_data['Rank'].min())
        temp['Lowest rank'].append(red_data['Rank'].max())   
    temp_df=pd.DataFrame(temp)
    display(temp_df)
ranks(df, 'Short Description')
ranks(df, 'Long Description')
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The small, fastest, secure Web Browser, best m...</td>
      <td>1.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Fast, simple, and secure. Google Chrome browse...</td>
      <td>1.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fast, safe web browser: free VPN, Ad blocker, ...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Puffin TV Browser - One App to Watch All Video...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Puffin TV Browser - One App to Watch All Video...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Free, safe internet browser! Private browsing,...</td>
      <td>1.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Fast &amp;amp; private web browser with ad blocker...</td>
      <td>17.0</td>
      <td>134.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Browse the web privately, search anonymously, ...</td>
      <td>1.0</td>
      <td>44.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Funny video, Football &amp;amp; celebrity news, Fa...</td>
      <td>6.0</td>
      <td>119.0</td>
    </tr>
  </tbody>
</table>
</div>



<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Long Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>One of the smallest, fastest, secure Web Brows...</td>
      <td>1.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Google Chrome is a fast, easy to use, and secu...</td>
      <td>1.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fast, safe and private, introducing the latest...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Opera is a fast, safe web browser for your And...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Puffin TV Browser is a special edition of Puff...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Puffin TV Browser is a special edition of Puff...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>Puffin TV Browser is a special edition of Puff...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>Puffin TV Browser is a special edition of Puff...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>8</th>
      <td>Privately browse the internet, for free! The B...</td>
      <td>1.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>9</th>
      <td>Vivaldi is a new private web browser with an i...</td>
      <td>17.0</td>
      <td>134.0</td>
    </tr>
    <tr>
      <th>10</th>
      <td>Tired of being tracked online? We can help. At...</td>
      <td>1.0</td>
      <td>44.0</td>
    </tr>
    <tr>
      <th>11</th>
      <td>Tired of being tracked online? We can help. At...</td>
      <td>1.0</td>
      <td>44.0</td>
    </tr>
    <tr>
      <th>12</th>
      <td>Phoenix Browser is a fast, safe web browser fo...</td>
      <td>6.0</td>
      <td>119.0</td>
    </tr>
  </tbody>
</table>
</div>


From the above output it is clear that, for every short/long description highest rank is close to 1, though lowest rank is varying a little. Thus, we can conclude that **the short/long desciption has no effect or there is no correlation between short/long description and the rank**.



Coming to the question of "does existence of a keyword in the description affect the rank?",  let us take few keywords and see how its presence affects the rank.



```python
def search(string, word):
    str_list=string.split()
    for i in range(10):
        if word.isin(str_list[i]):
            value= True
        else:
            value = False
    return value
    
def keyword_ranks(df2, column, keyword):
    re_df=df2[df2[column].str.contains(keyword, case=False)]
    print("Keyword:", keyword)
    ranks(re_df, column)
    print()
key_list=['fast', 'secure', 'simple', 'light', 'private', 'web', 'android', 'phone', 'VPN', 'mobile']
print('SHORT DESCRIPTIONS')
print('----------------------------')
for key in key_list:
    keyword_ranks(df, 'Short Description', key)
```

    SHORT DESCRIPTIONS
    ----------------------------
    Keyword: fast
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The small, fastest, secure Web Browser, best m...</td>
      <td>1.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Fast, simple, and secure. Google Chrome browse...</td>
      <td>1.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Fast, safe web browser: free VPN, Ad blocker, ...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Fast &amp;amp; private web browser with ad blocker...</td>
      <td>17.0</td>
      <td>134.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Funny video, Football &amp;amp; celebrity news, Fa...</td>
      <td>6.0</td>
      <td>119.0</td>
    </tr>
  </tbody>
</table>
</div>


    
    Keyword: secure
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The small, fastest, secure Web Browser, best m...</td>
      <td>1.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Fast, simple, and secure. Google Chrome browse...</td>
      <td>1.0</td>
      <td>20.0</td>
    </tr>
  </tbody>
</table>
</div>


    
    Keyword: simple
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Fast, simple, and secure. Google Chrome browse...</td>
      <td>1.0</td>
      <td>20.0</td>
    </tr>
  </tbody>
</table>
</div>


    
    Keyword: light
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
  </tbody>
</table>
</div>


    
    Keyword: private
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Free, safe internet browser! Private browsing,...</td>
      <td>1.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Fast &amp;amp; private web browser with ad blocker...</td>
      <td>17.0</td>
      <td>134.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Browse the web privately, search anonymously, ...</td>
      <td>1.0</td>
      <td>44.0</td>
    </tr>
  </tbody>
</table>
</div>


    
    Keyword: web
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The small, fastest, secure Web Browser, best m...</td>
      <td>1.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Fast, safe web browser: free VPN, Ad blocker, ...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Puffin TV Browser - One App to Watch All Video...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Puffin TV Browser - One App to Watch All Video...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Fast &amp;amp; private web browser with ad blocker...</td>
      <td>17.0</td>
      <td>134.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Browse the web privately, search anonymously, ...</td>
      <td>1.0</td>
      <td>44.0</td>
    </tr>
  </tbody>
</table>
</div>


    
    Keyword: android
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Fast, simple, and secure. Google Chrome browse...</td>
      <td>1.0</td>
      <td>20.0</td>
    </tr>
  </tbody>
</table>
</div>


    
    Keyword: phone
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Fast, simple, and secure. Google Chrome browse...</td>
      <td>1.0</td>
      <td>20.0</td>
    </tr>
  </tbody>
</table>
</div>


    
    Keyword: VPN
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Fast, safe web browser: free VPN, Ad blocker, ...</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
  </tbody>
</table>
</div>


    
    Keyword: mobile
    


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Short Description</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>The small, fastest, secure Web Browser, best m...</td>
      <td>1.0</td>
      <td>24.0</td>
    </tr>
  </tbody>
</table>
</div>


    
    

It can be seen from the above output that presence of keywords 'secure', 'simple', 'android', 'phone', 'VPN', 'mobile' can positively affect the rank.

The following output shows that for each App ID, the ranks vary pretty similarly. Hence APP ID **does not play any role in ranking**.


```python
ranks(df, 'App ID')
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>App ID</th>
      <th>Highest rank</th>
      <th>Lowest rank</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>net.fast.web.browser</td>
      <td>1.0</td>
      <td>24.0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>com.android.chrome</td>
      <td>1.0</td>
      <td>20.0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>com.opera.browser</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>com.cloudmosa.puffinTV</td>
      <td>1.0</td>
      <td>14.0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>com.brave.browser</td>
      <td>1.0</td>
      <td>13.0</td>
    </tr>
    <tr>
      <th>5</th>
      <td>com.vivaldi.browser</td>
      <td>17.0</td>
      <td>134.0</td>
    </tr>
    <tr>
      <th>6</th>
      <td>com.duckduckgo.mobile.android</td>
      <td>1.0</td>
      <td>44.0</td>
    </tr>
    <tr>
      <th>7</th>
      <td>com.transsion.phoenix</td>
      <td>6.0</td>
      <td>119.0</td>
    </tr>
  </tbody>
</table>
</div>


The following scatter plots of length of descriptions vs rank answers my question: **Does the length of descriptions affect the ranking?**.

There is no clear evidence of affect of length of descriptions on the rank!


```python
import matplotlib.pyplot as plt
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(8, 6))
ax1.scatter((df['Short Description'].apply(lambda x: len(x))), df['Rank'])
ax1.set_xlabel('Length of short description')
ax2.scatter((df['Long Description'].apply(lambda x: len(x))), df['Rank'])
ax2.set_xlabel('Length of long description')
fig.suptitle('Length of descriptions vs Rank')
plt.show()
```


    
![png](output_15_0.png)
    


# Part 2 (Two Questions)

## Check if the sentence is Grammatically correct: ##  
Please use any pre-trained model or use text from open datasets. Once done, please evaluate the English Grammar in the text column of the below dataset. 
https://drive.google.com/file/d/1LTI5KNqPrtxrYRgJk2AxI30KgYyNcRpD/view?usp=sharing

**Optional** - if you can indicate the grammatical accuracy of sentences in percentage or on number scale (1-10), that would be an added plus - but is not essential. 

**Solution** 
The following code uses the language-tool-python package which allows to create a client and get the grammatically correct text for an inputted text. We use the cosine similarity to check the grammatical accuracy of the original text.

The output of this code is a dataframe which consists the original text as one of the columns, the grammatical correctness of the text as second column and the grammatical accuracy of the original text



```python
import language_tool_python
import pandas as pd
import nltk
from nltk.corpus import stopwords 
from nltk.tokenize import word_tokenize 

def similarity(X,Y):
    X_list = word_tokenize(X)  
    Y_list = word_tokenize(Y) 
    sw = stopwords.words('english')  
    l1 =[];l2 =[] 
    X_set = {w for w in X_list if not w in sw}  
    Y_set = {w for w in Y_list if not w in sw} 
    rvector = X_set.union(Y_set)  
    for w in rvector: 
        if w in X_set: l1.append(1)
        else: l1.append(0) 
        if w in Y_set: l2.append(1) 
        else: l2.append(0) 
        c = 0
    for i in range(len(rvector)): 
        c+= l1[i]*l2[i] 
    if c==0:
        cosine = 0
    else:
        cosine = c / float((sum(l1)*sum(l2))**0.5) 
    return cosine

def corrected_text(text):
    tool = language_tool_python.LanguageTool('en-US')
    cor_text=tool.correct(text)
    tool.close()
    res='Grammatically incorrect'
    if cor_text==text:
        res='Grammatically correct'
    per=similarity(text, cor_text)*100
    return res, per

df=pd.read_csv(r'grammar\review_data.csv')[['text']]
df=df.head(5)
df['Correctness']=df['text'].apply(lambda x: corrected_text(x)[0])
df['Accuracy']=df['text'].apply(lambda x: corrected_text(x)[1])
display(df)
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>text</th>
      <th>Correctness</th>
      <th>Accuracy</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Anathi Khanyile</td>
      <td>Grammatically incorrect</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>Tony bahut funny hai Hill climbing racing my f...</td>
      <td>Grammatically incorrect</td>
      <td>66.666667</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Teturwu</td>
      <td>Grammatically incorrect</td>
      <td>0.000000</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Hoooooooooooyaaaaaaaaa what a game hoooooooooo...</td>
      <td>Grammatically correct</td>
      <td>100.000000</td>
    </tr>
    <tr>
      <th>4</th>
      <td>This game is nice</td>
      <td>Grammatically correct</td>
      <td>100.000000</td>
    </tr>
  </tbody>
</table>
</div>



