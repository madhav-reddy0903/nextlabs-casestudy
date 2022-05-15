---
title: "nextlabs-casestudy"
slug: "getting-started"
excerpt: "Solution to the case-study by Madhav Reddy"
hidden: false
createdAt: "2022-05-12T04:41:11.723Z"
updatedAt: "2022-05-15T07:49:33.262Z"
---
## Part 1 (Three Questions)


##1. Write a regex to extract all the numbers with orange color background from the below text in italics.*


  * {"orders":[{"id":**1**},{"id":**2**},{"id":**3**},{"id":**4**},{"id":**5**},{"id":**6**},{"id":**7**},{"id":**8**},{"id":**9**},{"id":**10**},{"id":**11**},{"id":**648**},{"id":**649**},{"id":**650**},{"id":**651**},{"id":**652**},{"id":**653**}],"errors":[{"code":**3**,"message":"[PHP Warning #2] count(): Parameter must be an array or an object that implements Countable (153)"}]}


The following is the python code to extract indicated numbers as a list.
[block:code]
{
  "codes": [
    {
      "code": "import re\nstring=\"\"\"{\"orders\":[{\"id\":1},{\"id\":2},{\"id\":3},{\"id\":4},{\"id\":5},{\"id\":6},\n{\"id\":7},{\"id\":8},{\"id\":9},{\"id\":10},{\"id\":11},{\"id\":648},{\"id\":649},{\"id\":650},\n{\"id\":651},{\"id\":652},{\"id\":653}],\"errors\":[{\"code\":3,\"message\":\n\"[PHP Warning #2] count(): Parameter must be an array or an object that \nimplements Countable (153)\"}]}\"\"\"\npattern=\":(\\d+)\"\nextracted_list=re.findall(pattern,string)\nprint(extracted_list)",
      "language": "python"
    }
  ]
}
[/block]
##2. Here’s the list of reviews of Chrome apps - scraped from Playstore. * [DataSet Link](https://drive.google.com/file/d/1SuiFw4MYxOBlqsRgyXLfch28-gzx8bX6/view?usp=sharing)

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
[block:code]
{
  "codes": [
    {
      "code": "import pandas as pd\nimport streamlit as st\nimport nltk\nnltk.downloader.download('vader_lexicon')\nfrom nltk.sentiment.vader import SentimentIntensityAnalyzer\nimport streamlit_authenticator as stauth\n\ndef identifier(df):\n    sentiments = SentimentIntensityAnalyzer()\n    data=df[['ID', 'Text', 'Star', 'User Name']]\n    data=data[data['Text'].notna()]\n    data[\"Positive\"] = [sentiments.polarity_scores(i)[\"pos\"] for i in data[\"Text\"]]\n    red_data=data.loc[(data[\"Positive\"]>0.5) & (data[\"Star\"]<3)]\n    output=red_data[['Text', 'Star', 'User Name']]\n    return output\n   \nnames = ['Madhav Reddy','Next Labs']\nusernames = ['madhav','nlabs']\npasswords = ['123456','456789']\nhashed_passwords = stauth.Hasher(passwords).generate()\nauthenticator = stauth.Authenticate(names,usernames,hashed_passwords,\n'some_cookie_name','some_signature_key',cookie_expiry_days=30)\nname, authentication_status, username = authenticator.login('Login','main')\nif authentication_status:\n    authenticator.logout('Logout', 'main')\n    st.write('Welcome *%s*' % (name))\n    html_temp = \"\"\"\n    <div>\n    <h1 style =\"color:white;text-align:center;\"> Incorrect ratings identifier </h1>\n    </div>\n    \"\"\"\n    st.markdown(html_temp, unsafe_allow_html = True)\n    uploaded_file = st.file_uploader(\"Upload the csv file\", type='csv')\n    if uploaded_file is not None:\n        df1=pd.read_csv(uploaded_file)\n        st.write(\"The following is the list of users whose reviews mismatch with the ratings\")\n        st.dataframe(identifier(df1))\nelif authentication_status == False:\n    st.error('Username/password is incorrect')\nelif authentication_status == None:\n    st.warning('Please enter your username and password')",
      "language": "python"
    }
  ]
}
[/block]
The login screen looks like this:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/9daaeb6-login_screen.png",
        "login screen.png",
        1917,
        992,
        "#121419"
      ],
      "sizing": "smart"
    }
  ]
}
[/block]
Once the required details are entered, it takes to the following page where the user will be given provision to upload the csv file.
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/445b576-upload_screen.png",
        "upload screen.png",
        1911,
        991,
        "#12141a"
      ]
    }
  ]
}
[/block]
Finally, once the user uploads the csv file, the app will display the list of user names along with ID, the review text and the rating where the reviews are good but the rating given is low (1 or 2 stars). The screen looks like this:
[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/7fef0d8-result.png",
        "result.png",
        1916,
        987,
        "#13151b"
      ]
    }
  ]
}
[/block]
## 3.	Ranking Data - Understanding the co-relation between keyword rankings with description or any other attribute. Here’s the dataset. 
https://drive.google.com/file/d/1yuDyU7EjJ8Nai83FDdIF2w4inm17NzBF/view?usp=sharing

**Suggested questions:**

1.	Is there any co-relation between short description, long description and ranking? Does the placement of keyword (for example - using a keyword in the first 10 words - have any co-relation with the ranking)?
2.	Does APP ID (Also known as package name) play any role in ranking?  
3.	Any other pattern or good questions that you can think of and answer?
[block:code]
{
  "codes": [
    {
      "code": "import pandas as pd\nimport streamlit as st\nimport nltk\nimport os\nos.chdir(r'C:\\Users\\madha\\My Drive\\Desktop\\Python\\Nextlabs')\nfrom nltk.corpus import stopwords \nfrom nltk.tokenize import word_tokenize \ndf=pd.read_csv(r'Ranking data\\browser_rankings_data.csv')\ndef ranks(df1, column):\n    temp={column:[], 'Highest rank':[], 'Lowest rank':[]}\n    for des in df1[column].unique():\n        red_data=df1[df1[column]== des]\n        temp[column].append(des)\n        temp['Highest rank'].append(red_data['Rank'].min())\n        temp['Lowest rank'].append(red_data['Rank'].max())   \n    temp_df=pd.DataFrame(temp)\n    print(temp_df)\nranks(df, 'Short Description')\nranks(df, 'Long Description')",
      "language": "python"
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/7967ce2-desc.png",
        "desc.png",
        588,
        670,
        "#f0f0f0"
      ]
    }
  ]
}
[/block]
From the above output it is clear that, for every short/long description highest rank is close to 1, though lowest rank is varying a little. Thus, we can conclude that **the short/long desciption has no effect or there is no correlation between short/long description and the rank**.

Coming to the question of "does existence of a keyword in the description affect the rank?",  let us take few keywords and see how its presence affects the rank.
[block:code]
{
  "codes": [
    {
      "code": "def search(string, word):\n    str_list=string.split()\n    for i in range(10):\n        if word.isin(str_list[i]):\n            value= True\n        else:\n            value = False\n    return value\n    \ndef keyword_ranks(df2, column, keyword):\n    re_df=df2[df2[column].str.contains(keyword, case=False)]\n    print(\"Keyword:\", keyword)\n    ranks(re_df, column)\n    print()\nkey_list=['fast', 'secure', 'simple', 'light', 'private', 'web', 'android', 'phone', 'VPN', 'mobile']\nprint('SHORT DESCRIPTIONS')\nprint('----------------------------')\nfor key in key_list:\n    keyword_ranks(df, 'Short Description', key)",
      "language": "python"
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/42b41e0-desc1.png",
        "desc1.png",
        620,
        804,
        "#f5f5f5"
      ]
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/0d6aa92-desc2.png",
        "desc2.png",
        664,
        758,
        "#f6f6f6"
      ]
    }
  ]
}
[/block]
It can be seen from the above output that presence of keywords 'secure', 'simple', 'android', 'phone', 'VPN', 'mobile' can positively affect the rank.

The following output shows that for each App ID, the ranks vary pretty similarly. Hence APP ID **does not play any role in ranking**.
[block:code]
{
  "codes": [
    {
      "code": "ranks(df, 'App ID')",
      "language": "python"
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/7057b12-appid.png",
        "appid.png",
        433,
        254,
        "#f2f2f2"
      ]
    }
  ]
}
[/block]
The following scatter plots of length of descriptions vs rank answers my question: **Does the length of descriptions affect the ranking?**.

There is no clear evidence of affect of length of descriptions on the rank!
[block:code]
{
  "codes": [
    {
      "code": "import matplotlib.pyplot as plt\nfig, (ax1, ax2) = plt.subplots(1, 2, figsize=(8, 6))\nax1.scatter((df['Short Description'].apply(lambda x: len(x))), df['Rank'])\nax1.set_xlabel('Length of short description')\nax2.scatter((df['Long Description'].apply(lambda x: len(x))), df['Rank'])\nax2.set_xlabel('Length of long description')\nfig.suptitle('Length of descriptions vs Rank')\nplt.show()",
      "language": "python"
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/3281f00-download.png",
        "download.png",
        494,
        414,
        "#e5ecf1"
      ]
    }
  ]
}
[/block]
## Part 2 (Two Questions)

## Check if the sentence is Grammatically correct: ##  
Please use any pre-trained model or use text from open datasets. Once done, please evaluate the English Grammar in the text column of the below dataset. 
https://drive.google.com/file/d/1LTI5KNqPrtxrYRgJk2AxI30KgYyNcRpD/view?usp=sharing

**Optional** - if you can indicate the grammatical accuracy of sentences in percentage or on number scale (1-10), that would be an added plus - but is not essential. 

**Solution** 
The following code uses the language-tool-python package which allows to create a client and get the grammatically correct text for an inputted text. We use the cosine similarity to check the grammatical accuracy of the original text.

The output of this code is a dataframe which consists the original text as one of the columns, the grammatical correctness of the text as second column and the grammatical accuracy of the original text.
[block:code]
{
  "codes": [
    {
      "code": "import language_tool_python\nimport pandas as pd\nimport nltk\nfrom nltk.corpus import stopwords \nfrom nltk.tokenize import word_tokenize \n\ndef similarity(X,Y):\n    X_list = word_tokenize(X)  \n    Y_list = word_tokenize(Y) \n    sw = stopwords.words('english')  \n    l1 =[];l2 =[] \n    X_set = {w for w in X_list if not w in sw}  \n    Y_set = {w for w in Y_list if not w in sw} \n    rvector = X_set.union(Y_set)  \n    for w in rvector: \n        if w in X_set: l1.append(1)\n        else: l1.append(0) \n        if w in Y_set: l2.append(1) \n        else: l2.append(0) \n        c = 0\n    for i in range(len(rvector)): \n        c+= l1[i]*l2[i] \n    if c==0:\n        cosine = 0\n    else:\n        cosine = c / float((sum(l1)*sum(l2))**0.5) \n    return cosine\n\ndef corrected_text(text):\n    tool = language_tool_python.LanguageTool('en-US')\n    cor_text=tool.correct(text)\n    tool.close()\n    res='Grammatically incorrect'\n    if cor_text==text:\n        res='Grammatically correct'\n    per=similarity(text, cor_text)*100\n    return res, per\n\ndf=pd.read_csv(r'grammar\\review_data.csv')[['text']]\ndf['Correctness']=df['text'].apply(lambda x: corrected_text(x)[0])\ndf['Accuracy']=df['text'].apply(lambda x: corrected_text(x)[1])\nprint(df.head())",
      "language": "python"
    }
  ]
}
[/block]

[block:image]
{
  "images": [
    {
      "image": [
        "https://files.readme.io/4c3cd74-grammar.png",
        "grammar.png",
        573,
        178,
        "#eeeeee"
      ]
    }
  ]
}
[/block]
##More Bonus points (You can write answers to these in ReadMe)

1.	Write about any difficult problem that you solved. (According to us difficult - is something which 90% of people would have only 10% probability in getting a similarly good solution). 

**Answer**



2.	Formally, a vector space V' is a subspace of a vector space V if
○	V' is a vector space
○	every element of V′ is also an element of V.
Note that ordered pairs of real numbers (a,b) a,b∈R form a vector space V. Which of the following is a subspace of V?
(a) The set of pairs (a, a + 1) for all real a
(b) The set of pairs (a, b) for all real a ≥ b
(c) The set of pairs (a, 2a) for all real a
(d) The set of pairs (a, b) for all non-negative real a,b

**Answer**
Since subspace is a vector space in itself, it should have the zero vector. The set in (a) is not a subspace as the zero vector (0,0) is not in the set. 

If V' is a subspace, then -v will be in V' for every v in V'. The set in (b) is not a subspace as (2,1) is in the set but -(2,1)= (-2, -1) is not in the set.

By the same reason the set in (d) is not a subspace.

Now, coming to the set in (c), clearly, the zero vector (0,0) is in the set. For any two vectors (a,2a) and (b,2b) in the set, we have p(a,2a)+q(b,2b) =(pa+qb, 2(pa+qb)) is also in the set. Thus the criterion for a subspace are satisfied. 

**Hence the set of pairs (a, 2a) for all real a, is the only set among the given sets, which is a subspace.**