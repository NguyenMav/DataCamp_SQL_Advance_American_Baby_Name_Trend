## Project: Analyzing American Baby Names Trend (SQL Advance)

## 1. Classic American names
<p>How have American baby name tastes changed since 1920?  Which names have remained popular for over 100 years, and how do those names compare to more recent top baby names? These are considerations for many new parents, but the skills we'll practice while answering these queries are broadly applicable. After all, understanding trends and popularity is important for many businesses, too! </p>
<p>We'll be working with data provided by the United States Social Security Administration, which lists first names along with the number and sex of babies they were given to in each year. For processing speed purposes, we've limited the dataset to first names which were given to over 5,000 American babies in a given year. Our data spans 101 years, from 1920 through 2020.</p>
<h3 id="baby_names"><code>baby_names</code></h3>
<table>
<thead>
<tr>
<th style="text-align:left;">column</th>
<th>type</th>
<th>meaning</th>
</tr>
</thead>
<tbody>
<tr>
<td style="text-align:left;"><code>year</code></td>
<td>int</td>
<td>year</td>
</tr>
<tr>
<td style="text-align:left;"><code>first_name</code></td>
<td>varchar</td>
<td>first name</td>
</tr>
<tr>
<td style="text-align:left;"><code>sex</code></td>
<td>varchar</td>
<td><code>sex</code> of babies given <code>first_name</code></td>
</tr>
<tr>
<td style="text-align:left;"><code>num</code></td>
<td>int</td>
<td>number of babies of <code>sex</code> given <code>first_name</code> in that <code>year</code></td>
</tr>
</tbody>
</table>
<p>Let's get oriented to American baby name tastes by looking at the names that have stood the test of time!</p>


```sql
%%sql
postgresql:///names
    
-- Select first names and the total babies with that first_name
-- Group by first_name and filter for those names that appear in all 101 years
-- Order by the total number of babies with that first_name, descending

SELECT first_name, 
    SUM(num)
FROM baby_names
GROUP BY first_name
HAVING COUNT(year) = 101
ORDER BY SUM(num) DESC;
```

    8 rows affected.





<table>
    <thead>
        <tr>
            <th>first_name</th>
            <th>sum</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>James</td>
            <td>4748138</td>
        </tr>
        <tr>
            <td>John</td>
            <td>4510721</td>
        </tr>
        <tr>
            <td>William</td>
            <td>3614424</td>
        </tr>
        <tr>
            <td>David</td>
            <td>3571498</td>
        </tr>
        <tr>
            <td>Joseph</td>
            <td>2361382</td>
        </tr>
        <tr>
            <td>Thomas</td>
            <td>2166802</td>
        </tr>
        <tr>
            <td>Charles</td>
            <td>2112352</td>
        </tr>
        <tr>
            <td>Elizabeth</td>
            <td>1436286</td>
        </tr>
    </tbody>
</table>




```python
%%nose
last_output = _

def test_output_type():
    assert str(type(last_output)) == "<class 'sql.run.ResultSet'>", \
    "Please ensure an SQL ResultSet is the output of the code cell." 

results = last_output.DataFrame()

def test_results():
    assert results.shape == (8, 2), \
    "The query should return eight rows and two columns."
    assert results.columns.tolist() == ["first_name", "sum"], \
    'The results should have two columns: "first_name" and "sum".'
    assert last_output.DataFrame().loc[0, 'first_name'] == 'James', \
    "The first_name in the first row should be James."
    assert last_output.DataFrame().loc[0, 'sum'] == 4748138, \
    "There should be 4,748,138 babies ever named James."
```






    2/2 tests passed




## 2. Timeless or trendy?
<p>Wow, it looks like there are a lot of timeless traditionally male names! Elizabeth is holding her own for the female names, too. </p>
<p>Now, let's broaden our understanding of the dataset by looking at all names. We'll attempt to capture the type of popularity that each name in the dataset enjoyed. Was the name classic and popular across many years or trendy, only popular for a few years? Let's find out. </p>


```sql
%%sql

-- Classify first names as 'Classic', 'Semi-classic', 'Semi-trendy', or 'Trendy'
-- Alias this column as popularity_type
-- Select first_name, the sum of babies who have ever had that name, and popularity_type
-- Order the results alphabetically by first_name

SELECT first_name, 
    sum(num), 
    CASE WHEN COUNT(year) > 80 THEN 'Classic'
    WHEN COUNT(year) > 50 THEN 'Semi-classic'
    WHEN COUNT(year) > 20 THEN 'Semi-trendy'
    WHEN COUNT(year) > 0 THEN 'Trendy' END AS popularity_type
FROM baby_names
GROUP BY first_name
ORDER BY first_name ASC;
```

     * postgresql:///names
    547 rows affected.





<table>
    <thead>
        <tr>
            <th>first_name</th>
            <th>sum</th>
            <th>popularity_type</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Aaliyah</td>
            <td>15870</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Aaron</td>
            <td>530592</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Abigail</td>
            <td>338485</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Adam</td>
            <td>497293</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Addison</td>
            <td>107433</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Adrian</td>
            <td>147741</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Aidan</td>
            <td>68566</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Aiden</td>
            <td>216194</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Alan</td>
            <td>162041</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Albert</td>
            <td>260945</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Alex</td>
            <td>158677</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Alexa</td>
            <td>33522</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Alexander</td>
            <td>579854</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Alexandra</td>
            <td>167122</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Alexandria</td>
            <td>5026</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Alexis</td>
            <td>282149</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Alfred</td>
            <td>16260</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Alice</td>
            <td>296559</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Alicia</td>
            <td>84579</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Allen</td>
            <td>10256</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Allison</td>
            <td>214995</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Alyssa</td>
            <td>269134</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Amanda</td>
            <td>699911</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Amber</td>
            <td>313418</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Amelia</td>
            <td>106381</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Amy</td>
            <td>569542</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Andrea</td>
            <td>321655</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Andrew</td>
            <td>1157548</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Angel</td>
            <td>157667</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Angela</td>
            <td>541553</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Angelina</td>
            <td>11337</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Anita</td>
            <td>44692</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ann</td>
            <td>336091</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Anna</td>
            <td>445496</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Anne</td>
            <td>70228</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Annette</td>
            <td>49954</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Annie</td>
            <td>95837</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Anthony</td>
            <td>1344352</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Antonio</td>
            <td>10097</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>April</td>
            <td>138714</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Aria</td>
            <td>52145</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ariana</td>
            <td>5497</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Arianna</td>
            <td>5270</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ariel</td>
            <td>5410</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Arthur</td>
            <td>309705</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Asher</td>
            <td>38156</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ashley</td>
            <td>798738</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ashton</td>
            <td>5436</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Aubrey</td>
            <td>72220</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Audrey</td>
            <td>48341</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Aurora</td>
            <td>5184</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Austin</td>
            <td>365295</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ava</td>
            <td>265126</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Avery</td>
            <td>112293</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ayden</td>
            <td>34244</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Bailey</td>
            <td>10219</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Barbara</td>
            <td>1343901</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Barry</td>
            <td>85434</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Beatrice</td>
            <td>27983</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Bella</td>
            <td>5127</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Benjamin</td>
            <td>627696</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Bentley</td>
            <td>16844</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Bernice</td>
            <td>46347</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Beth</td>
            <td>55228</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Betty</td>
            <td>893396</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Beverly</td>
            <td>310683</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Billy</td>
            <td>270759</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Blake</td>
            <td>48795</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Bobby</td>
            <td>203289</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Bonnie</td>
            <td>193352</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Bradley</td>
            <td>147275</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Brandi</td>
            <td>16199</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Brandon</td>
            <td>729832</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Brandy</td>
            <td>48762</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Brayden</td>
            <td>93754</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Brenda</td>
            <td>513283</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Brian</td>
            <td>1107302</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Briana</td>
            <td>5001</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Brianna</td>
            <td>210328</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Brittany</td>
            <td>326255</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Brittney</td>
            <td>37878</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Brody</td>
            <td>21815</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Brooke</td>
            <td>110847</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Brooklyn</td>
            <td>62260</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Bruce</td>
            <td>266549</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Bryan</td>
            <td>314250</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Caden</td>
            <td>5052</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Caitlin</td>
            <td>38501</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Caleb</td>
            <td>259439</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Cameron</td>
            <td>253711</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Camila</td>
            <td>51882</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Carl</td>
            <td>334800</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Carla</td>
            <td>22276</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Carlos</td>
            <td>118325</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Carol</td>
            <td>740607</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Carole</td>
            <td>21186</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Caroline</td>
            <td>5021</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Carolyn</td>
            <td>438382</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Carrie</td>
            <td>77899</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Carson</td>
            <td>25598</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Carter</td>
            <td>141274</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Cassandra</td>
            <td>42677</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Catherine</td>
            <td>345852</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Cathy</td>
            <td>119020</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Chad</td>
            <td>177923</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Charles</td>
            <td>2112352</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Charlotte</td>
            <td>141540</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Chase</td>
            <td>97684</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Chelsea</td>
            <td>100857</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Cheryl</td>
            <td>392691</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Chloe</td>
            <td>187720</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Chris</td>
            <td>48878</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Christian</td>
            <td>357457</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Christina</td>
            <td>366859</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Christine</td>
            <td>465653</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Christopher</td>
            <td>2012792</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Christy</td>
            <td>10235</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Cindy</td>
            <td>161070</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Claire</td>
            <td>10225</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Clara</td>
            <td>21336</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Clarence</td>
            <td>77134</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Cody</td>
            <td>225952</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Cole</td>
            <td>72461</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Colin</td>
            <td>5122</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Colton</td>
            <td>58377</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Connie</td>
            <td>179476</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Connor</td>
            <td>203106</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Cooper</td>
            <td>31011</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Corey</td>
            <td>70531</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Cory</td>
            <td>29454</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Courtney</td>
            <td>202829</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Craig</td>
            <td>190323</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Crystal</td>
            <td>239999</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Curtis</td>
            <td>48098</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Cynthia</td>
            <td>577859</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Dakota</td>
            <td>34334</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Dale</td>
            <td>130919</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Dana</td>
            <td>51558</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Daniel</td>
            <td>1824274</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Danielle</td>
            <td>299683</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Danny</td>
            <td>161078</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Darlene</td>
            <td>89561</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Darren</td>
            <td>5935</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Darryl</td>
            <td>10142</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>David</td>
            <td>3571498</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Dawn</td>
            <td>225877</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Debbie</td>
            <td>138846</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Deborah</td>
            <td>675049</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Debra</td>
            <td>508230</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Denise</td>
            <td>285039</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Dennis</td>
            <td>492221</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Derek</td>
            <td>105026</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Destiny</td>
            <td>100465</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Devin</td>
            <td>70280</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Diana</td>
            <td>172195</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Diane</td>
            <td>453135</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Diego</td>
            <td>46535</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Dillon</td>
            <td>5062</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Dolores</td>
            <td>101453</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Dominic</td>
            <td>67420</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Donald</td>
            <td>1280236</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Donna</td>
            <td>762594</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Doris</td>
            <td>336062</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Dorothy</td>
            <td>791084</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Douglas</td>
            <td>426439</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Dustin</td>
            <td>138651</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Dylan</td>
            <td>360776</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Earl</td>
            <td>74214</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Easton</td>
            <td>21820</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Edith</td>
            <td>53687</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Edna</td>
            <td>63698</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Edward</td>
            <td>1013143</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Elaine</td>
            <td>100359</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Eleanor</td>
            <td>119863</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Eli</td>
            <td>74938</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Elias</td>
            <td>16965</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Elijah</td>
            <td>277457</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Elizabeth</td>
            <td>1436286</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Ella</td>
            <td>154079</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ellen</td>
            <td>82403</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ellie</td>
            <td>26266</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Emily</td>
            <td>750420</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Emma</td>
            <td>448087</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Eric</td>
            <td>797880</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Erica</td>
            <td>156158</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Erin</td>
            <td>239718</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ernest</td>
            <td>43959</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Esther</td>
            <td>28040</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ethan</td>
            <td>408918</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ethel</td>
            <td>53359</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Eugene</td>
            <td>239512</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Evan</td>
            <td>203165</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Evelyn</td>
            <td>310824</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ezekiel</td>
            <td>5013</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ezra</td>
            <td>24632</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Faith</td>
            <td>27204</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Florence</td>
            <td>99171</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Frances</td>
            <td>348520</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Francis</td>
            <td>59097</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Frank</td>
            <td>596887</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Franklin</td>
            <td>5364</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Fred</td>
            <td>170837</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Gabriel</td>
            <td>270207</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Gabriella</td>
            <td>51486</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Gabrielle</td>
            <td>51144</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Gail</td>
            <td>118334</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Garrett</td>
            <td>15976</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Gary</td>
            <td>817491</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Gavin</td>
            <td>130460</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>George</td>
            <td>1032513</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Gerald</td>
            <td>327545</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Geraldine</td>
            <td>52850</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Gertrude</td>
            <td>16229</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Gianna</td>
            <td>7826</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Gina</td>
            <td>46380</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Gladys</td>
            <td>77627</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Glenn</td>
            <td>94437</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Gloria</td>
            <td>331698</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Grace</td>
            <td>254573</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Grayson</td>
            <td>61689</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Greg</td>
            <td>15849</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Gregory</td>
            <td>644286</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Hailey</td>
            <td>111571</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Haley</td>
            <td>106119</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Hannah</td>
            <td>392284</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Harold</td>
            <td>368463</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Harper</td>
            <td>86554</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Harry</td>
            <td>182312</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Hayden</td>
            <td>34724</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Hazel</td>
            <td>66103</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Heather</td>
            <td>468165</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Helen</td>
            <td>569998</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Henry</td>
            <td>429656</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Herbert</td>
            <td>52341</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Holly</td>
            <td>54634</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Howard</td>
            <td>157144</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Hudson</td>
            <td>43048</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Hunter</td>
            <td>220439</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ian</td>
            <td>159100</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Irene</td>
            <td>110116</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Isaac</td>
            <td>209563</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Isabella</td>
            <td>336924</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Isaiah</td>
            <td>200116</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jace</td>
            <td>23233</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jack</td>
            <td>552411</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Jackson</td>
            <td>219588</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jacob</td>
            <td>888209</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jacqueline</td>
            <td>223092</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jaden</td>
            <td>21777</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jaime</td>
            <td>13744</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>James</td>
            <td>4748138</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Jamie</td>
            <td>157417</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jane</td>
            <td>195627</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Janet</td>
            <td>444842</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Janice</td>
            <td>328335</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jared</td>
            <td>137848</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jasmine</td>
            <td>192464</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jason</td>
            <td>998257</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jaxon</td>
            <td>66542</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jaxson</td>
            <td>5061</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jay</td>
            <td>20682</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jayden</td>
            <td>213005</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jean</td>
            <td>363812</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jeff</td>
            <td>61390</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jeffery</td>
            <td>131241</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jeffrey</td>
            <td>907378</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jenna</td>
            <td>60548</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jennifer</td>
            <td>1404743</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jeremiah</td>
            <td>104792</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jeremy</td>
            <td>366271</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jerry</td>
            <td>494469</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jesse</td>
            <td>183645</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jessica</td>
            <td>994210</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jesus</td>
            <td>82359</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jill</td>
            <td>120405</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jim</td>
            <td>16588</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jimmy</td>
            <td>135558</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jo</td>
            <td>84238</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Joan</td>
            <td>413286</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Joanne</td>
            <td>77323</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Jocelyn</td>
            <td>5292</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Joe</td>
            <td>302127</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>John</td>
            <td>4510721</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Johnny</td>
            <td>138737</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jonathan</td>
            <td>772846</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Jordan</td>
            <td>426912</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Jose</td>
            <td>434805</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Joseph</td>
            <td>2361382</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Josephine</td>
            <td>69222</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Joshua</td>
            <td>1204236</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Josiah</td>
            <td>74001</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Joyce</td>
            <td>436267</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Juan</td>
            <td>189094</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Juanita</td>
            <td>20758</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Judith</td>
            <td>377449</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Judy</td>
            <td>329356</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Julia</td>
            <td>112397</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Julian</td>
            <td>137742</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Julie</td>
            <td>411989</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>June</td>
            <td>61668</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Justin</td>
            <td>729931</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Kaitlyn</td>
            <td>121541</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Karen</td>
            <td>892033</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Katelyn</td>
            <td>65023</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Katherine</td>
            <td>413349</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Kathleen</td>
            <td>516918</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Kathryn</td>
            <td>204173</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Kathy</td>
            <td>269922</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Katie</td>
            <td>97378</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Kayla</td>
            <td>294192</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Kaylee</td>
            <td>65209</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Keith</td>
            <td>313978</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Kelly</td>
            <td>417352</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Kelsey</td>
            <td>95434</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Kenneth</td>
            <td>1153846</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Kevin</td>
            <td>1140092</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Khloe</td>
            <td>5406</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Kim</td>
            <td>143648</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Kimberly</td>
            <td>767543</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Kristen</td>
            <td>127223</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Kristin</td>
            <td>81495</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Kristina</td>
            <td>10585</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Kristy</td>
            <td>5331</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Krystal</td>
            <td>5935</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Kyle</td>
            <td>394877</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Kylie</td>
            <td>16309</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Landon</td>
            <td>129558</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Larry</td>
            <td>700521</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Latoya</td>
            <td>5051</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Laura</td>
            <td>587161</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Lauren</td>
            <td>401513</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Laurie</td>
            <td>87568</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Lawrence</td>
            <td>307238</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Layla</td>
            <td>74474</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Leah</td>
            <td>63600</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Leo</td>
            <td>32643</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Leonard</td>
            <td>54127</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Leslie</td>
            <td>73075</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Levi</td>
            <td>91814</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Liam</td>
            <td>213059</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Lillian</td>
            <td>185120</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Lily</td>
            <td>115354</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Lincoln</td>
            <td>43147</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Linda</td>
            <td>1361021</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Lindsay</td>
            <td>69178</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Lindsey</td>
            <td>88669</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Lisa</td>
            <td>920119</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Logan</td>
            <td>316927</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Lois</td>
            <td>220781</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Lori</td>
            <td>289439</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Lorraine</td>
            <td>27338</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Louis</td>
            <td>115731</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Louise</td>
            <td>106456</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Lucas</td>
            <td>191033</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Lucille</td>
            <td>59379</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Luis</td>
            <td>144176</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Luke</td>
            <td>207795</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Luna</td>
            <td>27822</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Lynn</td>
            <td>97059</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Mackenzie</td>
            <td>46972</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Madeline</td>
            <td>26888</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Madison</td>
            <td>378127</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Makayla</td>
            <td>55446</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Marc</td>
            <td>5013</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Marcia</td>
            <td>15571</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Marcus</td>
            <td>53788</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Margaret</td>
            <td>806838</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Maria</td>
            <td>417502</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Mariah</td>
            <td>15723</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Marie</td>
            <td>249462</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Marilyn</td>
            <td>286722</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Marion</td>
            <td>50545</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Marissa</td>
            <td>29003</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Marjorie</td>
            <td>114386</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Mark</td>
            <td>1265910</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Marlene</td>
            <td>10368</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Marsha</td>
            <td>21303</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Martha</td>
            <td>359762</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Martin</td>
            <td>56023</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Mary</td>
            <td>3215850</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Mason</td>
            <td>263609</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Mateo</td>
            <td>45440</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Matthew</td>
            <td>1567204</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Maverick</td>
            <td>16863</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Maya</td>
            <td>5047</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Megan</td>
            <td>384668</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Melanie</td>
            <td>43995</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Melissa</td>
            <td>666250</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Mia</td>
            <td>216167</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Michael</td>
            <td>4278824</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Michele</td>
            <td>139690</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Michelle</td>
            <td>736097</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Mike</td>
            <td>97902</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Mila</td>
            <td>28047</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Mildred</td>
            <td>195666</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Miles</td>
            <td>5249</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Miranda</td>
            <td>11434</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Misty</td>
            <td>34935</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Mitchell</td>
            <td>5370</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Monica</td>
            <td>111143</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Morgan</td>
            <td>157320</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Nancy</td>
            <td>854761</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Natalie</td>
            <td>266634</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Nathan</td>
            <td>493746</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Nathaniel</td>
            <td>103671</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Nevaeh</td>
            <td>42926</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Nicholas</td>
            <td>777269</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Nicole</td>
            <td>533803</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Noah</td>
            <td>389490</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Nolan</td>
            <td>38147</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Nora</td>
            <td>34285</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Norma</td>
            <td>144522</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Norman</td>
            <td>47596</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Oliver</td>
            <td>107511</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Olivia</td>
            <td>429118</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Owen</td>
            <td>151569</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Paige</td>
            <td>48894</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Paisley</td>
            <td>5085</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Pamela</td>
            <td>524481</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Parker</td>
            <td>27453</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Patricia</td>
            <td>1479802</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Patrick</td>
            <td>559661</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Paul</td>
            <td>1218996</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Paula</td>
            <td>196090</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Pauline</td>
            <td>64073</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Peggy</td>
            <td>220586</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Penelope</td>
            <td>43409</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Penny</td>
            <td>10128</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Peter</td>
            <td>388795</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Peyton</td>
            <td>5315</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Philip</td>
            <td>100415</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Phillip</td>
            <td>86811</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Phyllis</td>
            <td>251517</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Rachel</td>
            <td>434626</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ralph</td>
            <td>273663</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Randall</td>
            <td>89055</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Randy</td>
            <td>215094</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Raymond</td>
            <td>541922</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Rebecca</td>
            <td>638458</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Regina</td>
            <td>10003</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Renee</td>
            <td>61185</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Rhonda</td>
            <td>157706</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Richard</td>
            <td>2414838</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Rick</td>
            <td>5462</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Ricky</td>
            <td>119547</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Riley</td>
            <td>73607</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Rita</td>
            <td>125877</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Robert</td>
            <td>4495199</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Robin</td>
            <td>210806</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Rodney</td>
            <td>125500</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Roger</td>
            <td>314531</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ronald</td>
            <td>974343</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Ronnie</td>
            <td>45564</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Rose</td>
            <td>248527</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Roy</td>
            <td>227920</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ruby</td>
            <td>93528</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Russell</td>
            <td>128647</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ruth</td>
            <td>475908</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Ryan</td>
            <td>926995</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Sabrina</td>
            <td>11589</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Sally</td>
            <td>30713</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Samantha</td>
            <td>514826</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Samuel</td>
            <td>539556</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Sandra</td>
            <td>783878</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Santiago</td>
            <td>5036</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Sara</td>
            <td>226696</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Sarah</td>
            <td>777519</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Savannah</td>
            <td>134405</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Scarlett</td>
            <td>54329</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Scott</td>
            <td>704468</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Sean</td>
            <td>372082</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Sebastian</td>
            <td>130244</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Seth</td>
            <td>35423</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Shane</td>
            <td>52869</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Shannon</td>
            <td>231132</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Sharon</td>
            <td>647989</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Shaun</td>
            <td>6107</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Shawn</td>
            <td>215326</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Sheila</td>
            <td>154361</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Shelby</td>
            <td>68474</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Sherri</td>
            <td>10819</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Sherry</td>
            <td>173913</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Shirley</td>
            <td>615887</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Sierra</td>
            <td>38980</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Skylar</td>
            <td>10408</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Sofia</td>
            <td>117208</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Sophia</td>
            <td>318523</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Stacey</td>
            <td>67483</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Stacy</td>
            <td>86835</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Stanley</td>
            <td>95152</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Stella</td>
            <td>10217</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Stephanie</td>
            <td>651976</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Stephen</td>
            <td>753958</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Steve</td>
            <td>114750</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Steven</td>
            <td>1216819</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Sue</td>
            <td>10450</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Susan</td>
            <td>1025728</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Suzanne</td>
            <td>109387</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Sydney</td>
            <td>117279</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tammy</td>
            <td>296905</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tanya</td>
            <td>22407</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tara</td>
            <td>107987</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Taylor</td>
            <td>323699</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Teresa</td>
            <td>298059</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Terri</td>
            <td>80961</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Terry</td>
            <td>346213</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Thelma</td>
            <td>74017</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Theodore</td>
            <td>29464</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Theresa</td>
            <td>225262</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Thomas</td>
            <td>2166802</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Tiffany</td>
            <td>283969</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Tim</td>
            <td>36165</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Timothy</td>
            <td>1001771</td>
            <td>Semi-classic</td>
        </tr>
        <tr>
            <td>Tina</td>
            <td>227252</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Todd</td>
            <td>207137</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tom</td>
            <td>5061</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tony</td>
            <td>96417</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tonya</td>
            <td>58234</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tracey</td>
            <td>16979</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tracy</td>
            <td>199320</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Travis</td>
            <td>218731</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Trevor</td>
            <td>76138</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Trinity</td>
            <td>16217</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tristan</td>
            <td>27212</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Troy</td>
            <td>82294</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Tyler</td>
            <td>548624</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Valerie</td>
            <td>70039</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Vanessa</td>
            <td>119596</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Vicki</td>
            <td>94504</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Vickie</td>
            <td>49252</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Victoria</td>
            <td>347794</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Vincent</td>
            <td>23419</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Violet</td>
            <td>10471</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Virginia</td>
            <td>441418</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Walter</td>
            <td>378194</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Wanda</td>
            <td>125458</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Warren</td>
            <td>13290</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Wayne</td>
            <td>211347</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Wendy</td>
            <td>159446</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Whitney</td>
            <td>43759</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>William</td>
            <td>3614424</td>
            <td>Classic</td>
        </tr>
        <tr>
            <td>Willie</td>
            <td>274564</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Wyatt</td>
            <td>128168</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Xavier</td>
            <td>51892</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Zachary</td>
            <td>483955</td>
            <td>Semi-trendy</td>
        </tr>
        <tr>
            <td>Zoe</td>
            <td>78773</td>
            <td>Trendy</td>
        </tr>
        <tr>
            <td>Zoey</td>
            <td>70140</td>
            <td>Trendy</td>
        </tr>
    </tbody>
</table>




```python
%%nose
last_output = _

def test_output_type():
    assert str(type(last_output)) == "<class 'sql.run.ResultSet'>", \
    "Please ensure an SQL ResultSet is the output of the code cell." 

results = last_output.DataFrame()

def test_results():
    assert results.shape == (547, 3), \
    "The query should return 547 rows and three columns."
    assert results.columns.tolist() == ["first_name", "sum", "popularity_type"], \
    'The results should have three columns: "first_name", "sum", and "popularity_type".'
    assert last_output.DataFrame().loc[0, 'first_name'] == 'Aaliyah', \
    "The first_name in the first row should be Aaliyah. Did you sort first names alphabetically?"
    assert last_output.DataFrame().loc[0, 'sum'] == 15870, \
    "There should be 15,870 babies ever named Aaliyah."
    assert last_output.DataFrame().loc[0, 'popularity_type'] == "Trendy", \
    "The name Aaliyah should be classified as 'Trendy'."
```






    2/2 tests passed




## 3. Top-ranked female names since 1920
<p>Did you find your favorite American celebrity's name on the popularity chart? Was it classic or trendy? How do you think the name Henry did? What about Jaxon?</p>
<p>Since we didn't get many traditionally female names in our classic American names search in the first task, let's limit our search to names which were given to female babies. </p>
<p>We can use this opportunity to practice window functions by assigning a rank to female names based on the number of babies that have ever been given that name. What are the top-ranked female names since 1920?</p>


```sql
%%sql

-- RANK names by the sum of babies who have ever had that name (descending), aliasing as name_rank
-- Select name_rank, first_name, and the sum of babies who have ever had that name
-- Filter the data for results where sex equals 'F'
-- Limit to ten results

SELECT RANK() OVER (ORDER BY sum(num) DESC) AS name_rank, 
    first_name, 
    sum(num)
FROM baby_names
WHERE sex = 'F'
GROUP BY first_name
LIMIT 10;
```

     * postgresql:///names
    10 rows affected.





<table>
    <thead>
        <tr>
            <th>name_rank</th>
            <th>first_name</th>
            <th>sum</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1</td>
            <td>Mary</td>
            <td>3215850</td>
        </tr>
        <tr>
            <td>2</td>
            <td>Patricia</td>
            <td>1479802</td>
        </tr>
        <tr>
            <td>3</td>
            <td>Elizabeth</td>
            <td>1436286</td>
        </tr>
        <tr>
            <td>4</td>
            <td>Jennifer</td>
            <td>1404743</td>
        </tr>
        <tr>
            <td>5</td>
            <td>Linda</td>
            <td>1361021</td>
        </tr>
        <tr>
            <td>6</td>
            <td>Barbara</td>
            <td>1343901</td>
        </tr>
        <tr>
            <td>7</td>
            <td>Susan</td>
            <td>1025728</td>
        </tr>
        <tr>
            <td>8</td>
            <td>Jessica</td>
            <td>994210</td>
        </tr>
        <tr>
            <td>9</td>
            <td>Lisa</td>
            <td>920119</td>
        </tr>
        <tr>
            <td>10</td>
            <td>Betty</td>
            <td>893396</td>
        </tr>
    </tbody>
</table>




```python
%%nose
last_output = _

def test_output_type():
    assert str(type(last_output)) == "<class 'sql.run.ResultSet'>", \
    "Please ensure an SQL ResultSet is the output of the code cell." 

results = last_output.DataFrame()

def test_results():
    assert results.shape == (10, 3), \
    "The query should return ten rows and three columns."
    assert set(results.columns.tolist()) == set(["first_name", "sum", "name_rank"]), \
    'The results should have three columns: "name_rank", "first_name", and "sum".'
    assert last_output.DataFrame().loc[0, 'first_name'] == 'Mary', \
    "The first_name in the first row should be Mary. Did you order so that names given to the most babies come first?"
    assert last_output.DataFrame().loc[0, 'sum'] == 3215850, \
    "There should be 3,215,850 babies ever named Mary."
    assert last_output.DataFrame().loc[0, 'name_rank'] == 1, \
    "The name Mary should be ranked number one."
```






    2/2 tests passed




## 4. Picking a baby name
<p>Perhaps a friend has heard of our work analyzing baby names and would like help choosing a name for her baby, a girl. She doesn't like any of the top-ranked names we found in the previous task. </p>
<p>She's set on a traditionally female name ending in the letter 'a' since she's heard that vowels in baby names are trendy. She's also looking for a name that has been popular in the years since 2015. </p>
<p>Let's see what we can do to find some options for this friend!</p>


```sql
%%sql
-- Select only the first_name column
-- Filter for results where sex is 'F', year is greater than 2015, and first_name ends in 'a'
-- Group by first_name and order by the total number of babies given that first_name

SELECT first_name
FROM baby_names
WHERE sex = 'F' AND year > 2015 AND first_name LIKE '%a'
GROUP BY first_name
ORDER BY SUM(num) DESC;
```

     * postgresql:///names
    19 rows affected.





<table>
    <thead>
        <tr>
            <th>first_name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Olivia</td>
        </tr>
        <tr>
            <td>Emma</td>
        </tr>
        <tr>
            <td>Ava</td>
        </tr>
        <tr>
            <td>Sophia</td>
        </tr>
        <tr>
            <td>Isabella</td>
        </tr>
        <tr>
            <td>Mia</td>
        </tr>
        <tr>
            <td>Amelia</td>
        </tr>
        <tr>
            <td>Ella</td>
        </tr>
        <tr>
            <td>Sofia</td>
        </tr>
        <tr>
            <td>Camila</td>
        </tr>
        <tr>
            <td>Aria</td>
        </tr>
        <tr>
            <td>Victoria</td>
        </tr>
        <tr>
            <td>Layla</td>
        </tr>
        <tr>
            <td>Nora</td>
        </tr>
        <tr>
            <td>Mila</td>
        </tr>
        <tr>
            <td>Luna</td>
        </tr>
        <tr>
            <td>Stella</td>
        </tr>
        <tr>
            <td>Gianna</td>
        </tr>
        <tr>
            <td>Aurora</td>
        </tr>
    </tbody>
</table>




```python
%%nose
last_output = _

def test_output_type():
    assert str(type(last_output)) == "<class 'sql.run.ResultSet'>", \
    "Please ensure an SQL ResultSet is the output of the code cell." 

results = last_output.DataFrame()

def test_results():
    assert results.shape == (19, 1), \
    "The query should return 19 rows and one column."
    assert results.columns.tolist() == ["first_name"], \
    'The results should have one column: "first_name".'
    assert last_output.DataFrame().loc[0, 'first_name'] == 'Olivia', \
    "The first_name in the first row should be Olivia."
```






    2/2 tests passed




## 5. The Olivia expansion
<p>Based on the results in the previous task, we can see that Olivia is the most popular female name ending in 'A' since 2015. When did the name Olivia become so popular?</p>
<p>Let's explore the rise of the name Olivia with the help of a window function.</p>


```sql
%%sql

-- Select year, first_name, num of Olivias in that year, and cumulative_olivias
-- Sum the cumulative babies who have been named Olivia up to that year; alias as cumulative_olivias
-- Filter so that only data for the name Olivia is returned.
-- Order by year from the earliest year to most recent

SELECT year, 
    first_name, 
    num, 
    SUM(num) OVER (ORDER BY year) AS cumulative_olivias
FROM baby_names
WHERE first_name = 'Olivia'
ORDER BY year ASC;
```

     * postgresql:///names
    30 rows affected.





<table>
    <thead>
        <tr>
            <th>year</th>
            <th>first_name</th>
            <th>num</th>
            <th>cumulative_olivias</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1991</td>
            <td>Olivia</td>
            <td>5601</td>
            <td>5601</td>
        </tr>
        <tr>
            <td>1992</td>
            <td>Olivia</td>
            <td>5809</td>
            <td>11410</td>
        </tr>
        <tr>
            <td>1993</td>
            <td>Olivia</td>
            <td>6340</td>
            <td>17750</td>
        </tr>
        <tr>
            <td>1994</td>
            <td>Olivia</td>
            <td>6434</td>
            <td>24184</td>
        </tr>
        <tr>
            <td>1995</td>
            <td>Olivia</td>
            <td>7624</td>
            <td>31808</td>
        </tr>
        <tr>
            <td>1996</td>
            <td>Olivia</td>
            <td>8124</td>
            <td>39932</td>
        </tr>
        <tr>
            <td>1997</td>
            <td>Olivia</td>
            <td>9477</td>
            <td>49409</td>
        </tr>
        <tr>
            <td>1998</td>
            <td>Olivia</td>
            <td>10610</td>
            <td>60019</td>
        </tr>
        <tr>
            <td>1999</td>
            <td>Olivia</td>
            <td>11255</td>
            <td>71274</td>
        </tr>
        <tr>
            <td>2000</td>
            <td>Olivia</td>
            <td>12852</td>
            <td>84126</td>
        </tr>
        <tr>
            <td>2001</td>
            <td>Olivia</td>
            <td>13977</td>
            <td>98103</td>
        </tr>
        <tr>
            <td>2002</td>
            <td>Olivia</td>
            <td>14630</td>
            <td>112733</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Olivia</td>
            <td>16152</td>
            <td>128885</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Olivia</td>
            <td>16106</td>
            <td>144991</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>Olivia</td>
            <td>15694</td>
            <td>160685</td>
        </tr>
        <tr>
            <td>2006</td>
            <td>Olivia</td>
            <td>15501</td>
            <td>176186</td>
        </tr>
        <tr>
            <td>2007</td>
            <td>Olivia</td>
            <td>16584</td>
            <td>192770</td>
        </tr>
        <tr>
            <td>2008</td>
            <td>Olivia</td>
            <td>17084</td>
            <td>209854</td>
        </tr>
        <tr>
            <td>2009</td>
            <td>Olivia</td>
            <td>17438</td>
            <td>227292</td>
        </tr>
        <tr>
            <td>2010</td>
            <td>Olivia</td>
            <td>17029</td>
            <td>244321</td>
        </tr>
        <tr>
            <td>2011</td>
            <td>Olivia</td>
            <td>17327</td>
            <td>261648</td>
        </tr>
        <tr>
            <td>2012</td>
            <td>Olivia</td>
            <td>17320</td>
            <td>278968</td>
        </tr>
        <tr>
            <td>2013</td>
            <td>Olivia</td>
            <td>18439</td>
            <td>297407</td>
        </tr>
        <tr>
            <td>2014</td>
            <td>Olivia</td>
            <td>19823</td>
            <td>317230</td>
        </tr>
        <tr>
            <td>2015</td>
            <td>Olivia</td>
            <td>19710</td>
            <td>336940</td>
        </tr>
        <tr>
            <td>2016</td>
            <td>Olivia</td>
            <td>19380</td>
            <td>356320</td>
        </tr>
        <tr>
            <td>2017</td>
            <td>Olivia</td>
            <td>18744</td>
            <td>375064</td>
        </tr>
        <tr>
            <td>2018</td>
            <td>Olivia</td>
            <td>18011</td>
            <td>393075</td>
        </tr>
        <tr>
            <td>2019</td>
            <td>Olivia</td>
            <td>18508</td>
            <td>411583</td>
        </tr>
        <tr>
            <td>2020</td>
            <td>Olivia</td>
            <td>17535</td>
            <td>429118</td>
        </tr>
    </tbody>
</table>




```python
%%nose
last_output = _

def test_output_type():
    assert str(type(last_output)) == "<class 'sql.run.ResultSet'>", \
    "Please ensure an SQL ResultSet is the output of the code cell." 

results = last_output.DataFrame()

def test_results():
    assert results.shape == (30, 4), \
    "The query should return thirty rows and four columns."
    assert set(results.columns.tolist()) == set(["year", "first_name", "num", "cumulative_olivias"]), \
    'The results should have four columns: "year", "first_name", "num", and "cumulative_olivias".'
    assert last_output.DataFrame().loc[0, 'first_name'] == 'Olivia', \
    "The first_name in the first row should be Olivia. Did you filter so that results only include data for the name Olivia?"
    assert last_output.DataFrame().loc[0, 'num'] == 5601, \
    "In 1991, there should have been 5,601 female babies named Olivia."
    assert last_output.DataFrame().loc[0, 'year'] == 1991, \
    "1991 should be the first year that Olivia appears in the results. Did you sort by year in ascending order?"
    assert last_output.DataFrame().loc[1, 'cumulative_olivias'] == 11410, \
    "In 1992, the cumulative_olivias column should read 11,410."
```






    2/2 tests passed




## 6. Many males with the same name
<p>Wow, Olivia has had a meteoric rise! Let's take a look at traditionally male names now. We saw in the first task that there are nine traditionally male names given to at least 5,000 babies every single year in our 101-year dataset! Those names are classics, but showing up in the dataset every year doesn't necessarily mean that the timeless names were the most popular. Let's explore popular male names a little further.</p>
<p>In the next two tasks, we will build up to listing every year along with the most popular male name in that year. This presents a common problem: how do we find the greatest X in a group? Or, in the context of this problem, how do we find the male name given to the highest number of babies in a year? </p>
<p>In SQL, one approach is to use a subquery. We can first write a query that selects the <code>year</code> and the maximum <code>num</code> of babies given any single male name in that year. For example, in 1989, the male name given to the highest number of babies was given to 65,339 babies. We'll write this query in this task. In the next task, we can use the code from this task as a subquery to look up the <code>first_name</code> that was given to 65,339 babies in 1989… as well as the top male first name for all other years!</p>


```sql
%%sql

-- Select year and maximum number of babies given any one male name in that year, aliased as max_num
-- Filter the data to include only results where sex equals 'M'
SELECT year, MAX(num) AS max_num
FROM baby_names
WHERE sex = 'M'
GROUP BY year;
```

     * postgresql:///names
    101 rows affected.





<table>
    <thead>
        <tr>
            <th>year</th>
            <th>max_num</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>1970</td>
            <td>85291</td>
        </tr>
        <tr>
            <td>2000</td>
            <td>34483</td>
        </tr>
        <tr>
            <td>1947</td>
            <td>94764</td>
        </tr>
        <tr>
            <td>1962</td>
            <td>85041</td>
        </tr>
        <tr>
            <td>1975</td>
            <td>68451</td>
        </tr>
        <tr>
            <td>1980</td>
            <td>68704</td>
        </tr>
        <tr>
            <td>1931</td>
            <td>60518</td>
        </tr>
        <tr>
            <td>1981</td>
            <td>68776</td>
        </tr>
        <tr>
            <td>2013</td>
            <td>18266</td>
        </tr>
        <tr>
            <td>1972</td>
            <td>71401</td>
        </tr>
        <tr>
            <td>1956</td>
            <td>90665</td>
        </tr>
        <tr>
            <td>2007</td>
            <td>24292</td>
        </tr>
        <tr>
            <td>1948</td>
            <td>88589</td>
        </tr>
        <tr>
            <td>1984</td>
            <td>67745</td>
        </tr>
        <tr>
            <td>1957</td>
            <td>92718</td>
        </tr>
        <tr>
            <td>1961</td>
            <td>86917</td>
        </tr>
        <tr>
            <td>2002</td>
            <td>30579</td>
        </tr>
        <tr>
            <td>1925</td>
            <td>60897</td>
        </tr>
        <tr>
            <td>1992</td>
            <td>54397</td>
        </tr>
        <tr>
            <td>2008</td>
            <td>22603</td>
        </tr>
        <tr>
            <td>1958</td>
            <td>90564</td>
        </tr>
        <tr>
            <td>1971</td>
            <td>77599</td>
        </tr>
        <tr>
            <td>1985</td>
            <td>64924</td>
        </tr>
        <tr>
            <td>1926</td>
            <td>61130</td>
        </tr>
        <tr>
            <td>1988</td>
            <td>64150</td>
        </tr>
        <tr>
            <td>1929</td>
            <td>59804</td>
        </tr>
        <tr>
            <td>1963</td>
            <td>83778</td>
        </tr>
        <tr>
            <td>1928</td>
            <td>60703</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>29643</td>
        </tr>
        <tr>
            <td>1930</td>
            <td>62149</td>
        </tr>
        <tr>
            <td>1951</td>
            <td>87261</td>
        </tr>
        <tr>
            <td>1940</td>
            <td>62476</td>
        </tr>
        <tr>
            <td>1982</td>
            <td>68244</td>
        </tr>
        <tr>
            <td>1920</td>
            <td>56914</td>
        </tr>
        <tr>
            <td>1999</td>
            <td>35367</td>
        </tr>
        <tr>
            <td>1952</td>
            <td>87063</td>
        </tr>
        <tr>
            <td>2020</td>
            <td>19659</td>
        </tr>
        <tr>
            <td>1946</td>
            <td>87439</td>
        </tr>
        <tr>
            <td>1968</td>
            <td>81995</td>
        </tr>
        <tr>
            <td>1996</td>
            <td>38365</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>25837</td>
        </tr>
        <tr>
            <td>1923</td>
            <td>57469</td>
        </tr>
        <tr>
            <td>2009</td>
            <td>21184</td>
        </tr>
        <tr>
            <td>1924</td>
            <td>60801</td>
        </tr>
        <tr>
            <td>1954</td>
            <td>88576</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>27886</td>
        </tr>
        <tr>
            <td>1938</td>
            <td>62269</td>
        </tr>
        <tr>
            <td>1942</td>
            <td>77174</td>
        </tr>
        <tr>
            <td>1966</td>
            <td>79990</td>
        </tr>
        <tr>
            <td>1998</td>
            <td>36616</td>
        </tr>
        <tr>
            <td>1974</td>
            <td>67580</td>
        </tr>
        <tr>
            <td>1949</td>
            <td>86865</td>
        </tr>
        <tr>
            <td>1990</td>
            <td>65302</td>
        </tr>
        <tr>
            <td>1995</td>
            <td>41399</td>
        </tr>
        <tr>
            <td>1973</td>
            <td>67842</td>
        </tr>
        <tr>
            <td>1927</td>
            <td>61671</td>
        </tr>
        <tr>
            <td>1941</td>
            <td>66743</td>
        </tr>
        <tr>
            <td>1977</td>
            <td>67609</td>
        </tr>
        <tr>
            <td>2001</td>
            <td>32554</td>
        </tr>
        <tr>
            <td>1997</td>
            <td>37549</td>
        </tr>
        <tr>
            <td>2014</td>
            <td>19319</td>
        </tr>
        <tr>
            <td>1965</td>
            <td>81021</td>
        </tr>
        <tr>
            <td>1935</td>
            <td>56522</td>
        </tr>
        <tr>
            <td>1944</td>
            <td>76954</td>
        </tr>
        <tr>
            <td>1994</td>
            <td>44472</td>
        </tr>
        <tr>
            <td>2016</td>
            <td>19154</td>
        </tr>
        <tr>
            <td>1960</td>
            <td>85933</td>
        </tr>
        <tr>
            <td>1987</td>
            <td>63654</td>
        </tr>
        <tr>
            <td>1978</td>
            <td>67157</td>
        </tr>
        <tr>
            <td>2018</td>
            <td>19924</td>
        </tr>
        <tr>
            <td>2006</td>
            <td>24850</td>
        </tr>
        <tr>
            <td>1921</td>
            <td>58215</td>
        </tr>
        <tr>
            <td>1993</td>
            <td>49554</td>
        </tr>
        <tr>
            <td>1964</td>
            <td>82642</td>
        </tr>
        <tr>
            <td>1943</td>
            <td>80274</td>
        </tr>
        <tr>
            <td>1937</td>
            <td>61842</td>
        </tr>
        <tr>
            <td>1986</td>
            <td>64224</td>
        </tr>
        <tr>
            <td>1953</td>
            <td>86247</td>
        </tr>
        <tr>
            <td>1959</td>
            <td>85224</td>
        </tr>
        <tr>
            <td>1976</td>
            <td>66947</td>
        </tr>
        <tr>
            <td>1989</td>
            <td>65399</td>
        </tr>
        <tr>
            <td>2012</td>
            <td>19088</td>
        </tr>
        <tr>
            <td>2011</td>
            <td>20378</td>
        </tr>
        <tr>
            <td>2019</td>
            <td>20555</td>
        </tr>
        <tr>
            <td>1955</td>
            <td>88372</td>
        </tr>
        <tr>
            <td>1939</td>
            <td>59653</td>
        </tr>
        <tr>
            <td>1979</td>
            <td>67742</td>
        </tr>
        <tr>
            <td>1991</td>
            <td>60793</td>
        </tr>
        <tr>
            <td>2017</td>
            <td>18824</td>
        </tr>
        <tr>
            <td>1969</td>
            <td>85201</td>
        </tr>
        <tr>
            <td>2015</td>
            <td>19650</td>
        </tr>
        <tr>
            <td>2010</td>
            <td>22139</td>
        </tr>
        <tr>
            <td>1932</td>
            <td>59265</td>
        </tr>
        <tr>
            <td>1967</td>
            <td>82440</td>
        </tr>
        <tr>
            <td>1922</td>
            <td>57280</td>
        </tr>
        <tr>
            <td>1933</td>
            <td>54223</td>
        </tr>
        <tr>
            <td>1934</td>
            <td>55834</td>
        </tr>
        <tr>
            <td>1936</td>
            <td>58499</td>
        </tr>
        <tr>
            <td>1945</td>
            <td>74460</td>
        </tr>
        <tr>
            <td>1950</td>
            <td>86229</td>
        </tr>
        <tr>
            <td>1983</td>
            <td>68010</td>
        </tr>
    </tbody>
</table>




```python
%%nose
import numpy as np
last_output = _

def test_output_type():
    assert str(type(last_output)) == "<class 'sql.run.ResultSet'>", \
    "Please ensure an SQL ResultSet is the output of the code cell." 

results = last_output.DataFrame()

def test_results():
    assert results.shape == (101, 2), \
    "The query should return 101 rows and two columns."
    assert set(results.columns.tolist()) == set(["year", "max_num"]), \
    'The results should have two columns: "year" and "max_num".'
    assert last_output.DataFrame().loc[list(np.where(last_output.DataFrame() == 1964)[0])[0], 'max_num'] == 82642, \
    "In 1964, the name given to the most babies was given 82,642 times."
```






    2/2 tests passed




## 7. Top male names over the years
<p>In the previous task, we found the maximum number of babies given any one male name in each year. Incredibly, the most popular name each year varied from being given to less than 20,000 babies to being given to more than 90,000! </p>
<p>In this task, we find out what that top male name is for each year in our dataset. </p>


```sql
%%sql

-- Select year, first_name given to the largest number of male babies, and num of babies given that name
-- Join baby_names to the code in the last task as a subquery
-- Order results by year descending

SELECT baby_names.year, baby_names.first_name, baby_names.num 
FROM baby_names 
INNER JOIN ( 
    SELECT year, MAX(num) AS max_num
    FROM baby_names
    WHERE sex = 'M'
    GROUP BY year) AS m
ON baby_names.year = m.year AND baby_names.num = m.max_num
ORDER BY year DESC;
```

     * postgresql:///names
    101 rows affected.





<table>
    <thead>
        <tr>
            <th>year</th>
            <th>first_name</th>
            <th>num</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>2020</td>
            <td>Liam</td>
            <td>19659</td>
        </tr>
        <tr>
            <td>2019</td>
            <td>Liam</td>
            <td>20555</td>
        </tr>
        <tr>
            <td>2018</td>
            <td>Liam</td>
            <td>19924</td>
        </tr>
        <tr>
            <td>2017</td>
            <td>Liam</td>
            <td>18824</td>
        </tr>
        <tr>
            <td>2016</td>
            <td>Noah</td>
            <td>19154</td>
        </tr>
        <tr>
            <td>2015</td>
            <td>Noah</td>
            <td>19650</td>
        </tr>
        <tr>
            <td>2014</td>
            <td>Noah</td>
            <td>19319</td>
        </tr>
        <tr>
            <td>2013</td>
            <td>Noah</td>
            <td>18266</td>
        </tr>
        <tr>
            <td>2012</td>
            <td>Jacob</td>
            <td>19088</td>
        </tr>
        <tr>
            <td>2011</td>
            <td>Jacob</td>
            <td>20378</td>
        </tr>
        <tr>
            <td>2010</td>
            <td>Jacob</td>
            <td>22139</td>
        </tr>
        <tr>
            <td>2009</td>
            <td>Jacob</td>
            <td>21184</td>
        </tr>
        <tr>
            <td>2008</td>
            <td>Jacob</td>
            <td>22603</td>
        </tr>
        <tr>
            <td>2007</td>
            <td>Jacob</td>
            <td>24292</td>
        </tr>
        <tr>
            <td>2006</td>
            <td>Jacob</td>
            <td>24850</td>
        </tr>
        <tr>
            <td>2005</td>
            <td>Jacob</td>
            <td>25837</td>
        </tr>
        <tr>
            <td>2004</td>
            <td>Jacob</td>
            <td>27886</td>
        </tr>
        <tr>
            <td>2003</td>
            <td>Jacob</td>
            <td>29643</td>
        </tr>
        <tr>
            <td>2002</td>
            <td>Jacob</td>
            <td>30579</td>
        </tr>
        <tr>
            <td>2001</td>
            <td>Jacob</td>
            <td>32554</td>
        </tr>
        <tr>
            <td>2000</td>
            <td>Jacob</td>
            <td>34483</td>
        </tr>
        <tr>
            <td>1999</td>
            <td>Jacob</td>
            <td>35367</td>
        </tr>
        <tr>
            <td>1998</td>
            <td>Michael</td>
            <td>36616</td>
        </tr>
        <tr>
            <td>1997</td>
            <td>Michael</td>
            <td>37549</td>
        </tr>
        <tr>
            <td>1996</td>
            <td>Michael</td>
            <td>38365</td>
        </tr>
        <tr>
            <td>1995</td>
            <td>Michael</td>
            <td>41399</td>
        </tr>
        <tr>
            <td>1994</td>
            <td>Michael</td>
            <td>44472</td>
        </tr>
        <tr>
            <td>1993</td>
            <td>Michael</td>
            <td>49554</td>
        </tr>
        <tr>
            <td>1992</td>
            <td>Michael</td>
            <td>54397</td>
        </tr>
        <tr>
            <td>1991</td>
            <td>Michael</td>
            <td>60793</td>
        </tr>
        <tr>
            <td>1990</td>
            <td>Michael</td>
            <td>65302</td>
        </tr>
        <tr>
            <td>1989</td>
            <td>Michael</td>
            <td>65399</td>
        </tr>
        <tr>
            <td>1988</td>
            <td>Michael</td>
            <td>64150</td>
        </tr>
        <tr>
            <td>1987</td>
            <td>Michael</td>
            <td>63654</td>
        </tr>
        <tr>
            <td>1986</td>
            <td>Michael</td>
            <td>64224</td>
        </tr>
        <tr>
            <td>1985</td>
            <td>Michael</td>
            <td>64924</td>
        </tr>
        <tr>
            <td>1984</td>
            <td>Michael</td>
            <td>67745</td>
        </tr>
        <tr>
            <td>1983</td>
            <td>Michael</td>
            <td>68010</td>
        </tr>
        <tr>
            <td>1982</td>
            <td>Michael</td>
            <td>68244</td>
        </tr>
        <tr>
            <td>1981</td>
            <td>Michael</td>
            <td>68776</td>
        </tr>
        <tr>
            <td>1980</td>
            <td>Michael</td>
            <td>68704</td>
        </tr>
        <tr>
            <td>1979</td>
            <td>Michael</td>
            <td>67742</td>
        </tr>
        <tr>
            <td>1978</td>
            <td>Michael</td>
            <td>67157</td>
        </tr>
        <tr>
            <td>1977</td>
            <td>Michael</td>
            <td>67609</td>
        </tr>
        <tr>
            <td>1976</td>
            <td>Michael</td>
            <td>66947</td>
        </tr>
        <tr>
            <td>1975</td>
            <td>Michael</td>
            <td>68451</td>
        </tr>
        <tr>
            <td>1974</td>
            <td>Michael</td>
            <td>67580</td>
        </tr>
        <tr>
            <td>1973</td>
            <td>Michael</td>
            <td>67842</td>
        </tr>
        <tr>
            <td>1972</td>
            <td>Michael</td>
            <td>71401</td>
        </tr>
        <tr>
            <td>1971</td>
            <td>Michael</td>
            <td>77599</td>
        </tr>
        <tr>
            <td>1970</td>
            <td>Michael</td>
            <td>85291</td>
        </tr>
        <tr>
            <td>1969</td>
            <td>Michael</td>
            <td>85201</td>
        </tr>
        <tr>
            <td>1968</td>
            <td>Michael</td>
            <td>81995</td>
        </tr>
        <tr>
            <td>1967</td>
            <td>Michael</td>
            <td>82440</td>
        </tr>
        <tr>
            <td>1966</td>
            <td>Michael</td>
            <td>79990</td>
        </tr>
        <tr>
            <td>1965</td>
            <td>Michael</td>
            <td>81021</td>
        </tr>
        <tr>
            <td>1964</td>
            <td>Michael</td>
            <td>82642</td>
        </tr>
        <tr>
            <td>1963</td>
            <td>Michael</td>
            <td>83778</td>
        </tr>
        <tr>
            <td>1962</td>
            <td>Michael</td>
            <td>85041</td>
        </tr>
        <tr>
            <td>1961</td>
            <td>Michael</td>
            <td>86917</td>
        </tr>
        <tr>
            <td>1960</td>
            <td>David</td>
            <td>85933</td>
        </tr>
        <tr>
            <td>1959</td>
            <td>Michael</td>
            <td>85224</td>
        </tr>
        <tr>
            <td>1958</td>
            <td>Michael</td>
            <td>90564</td>
        </tr>
        <tr>
            <td>1957</td>
            <td>Michael</td>
            <td>92718</td>
        </tr>
        <tr>
            <td>1956</td>
            <td>Michael</td>
            <td>90665</td>
        </tr>
        <tr>
            <td>1955</td>
            <td>Michael</td>
            <td>88372</td>
        </tr>
        <tr>
            <td>1954</td>
            <td>Michael</td>
            <td>88576</td>
        </tr>
        <tr>
            <td>1953</td>
            <td>Robert</td>
            <td>86247</td>
        </tr>
        <tr>
            <td>1952</td>
            <td>James</td>
            <td>87063</td>
        </tr>
        <tr>
            <td>1951</td>
            <td>James</td>
            <td>87261</td>
        </tr>
        <tr>
            <td>1950</td>
            <td>James</td>
            <td>86229</td>
        </tr>
        <tr>
            <td>1949</td>
            <td>James</td>
            <td>86865</td>
        </tr>
        <tr>
            <td>1948</td>
            <td>James</td>
            <td>88589</td>
        </tr>
        <tr>
            <td>1947</td>
            <td>James</td>
            <td>94764</td>
        </tr>
        <tr>
            <td>1946</td>
            <td>James</td>
            <td>87439</td>
        </tr>
        <tr>
            <td>1945</td>
            <td>James</td>
            <td>74460</td>
        </tr>
        <tr>
            <td>1944</td>
            <td>James</td>
            <td>76954</td>
        </tr>
        <tr>
            <td>1943</td>
            <td>James</td>
            <td>80274</td>
        </tr>
        <tr>
            <td>1942</td>
            <td>James</td>
            <td>77174</td>
        </tr>
        <tr>
            <td>1941</td>
            <td>James</td>
            <td>66743</td>
        </tr>
        <tr>
            <td>1940</td>
            <td>James</td>
            <td>62476</td>
        </tr>
        <tr>
            <td>1939</td>
            <td>Robert</td>
            <td>59653</td>
        </tr>
        <tr>
            <td>1938</td>
            <td>Robert</td>
            <td>62269</td>
        </tr>
        <tr>
            <td>1937</td>
            <td>Robert</td>
            <td>61842</td>
        </tr>
        <tr>
            <td>1936</td>
            <td>Robert</td>
            <td>58499</td>
        </tr>
        <tr>
            <td>1935</td>
            <td>Robert</td>
            <td>56522</td>
        </tr>
        <tr>
            <td>1934</td>
            <td>Robert</td>
            <td>55834</td>
        </tr>
        <tr>
            <td>1933</td>
            <td>Robert</td>
            <td>54223</td>
        </tr>
        <tr>
            <td>1932</td>
            <td>Robert</td>
            <td>59265</td>
        </tr>
        <tr>
            <td>1931</td>
            <td>Robert</td>
            <td>60518</td>
        </tr>
        <tr>
            <td>1930</td>
            <td>Robert</td>
            <td>62149</td>
        </tr>
        <tr>
            <td>1929</td>
            <td>Robert</td>
            <td>59804</td>
        </tr>
        <tr>
            <td>1928</td>
            <td>Robert</td>
            <td>60703</td>
        </tr>
        <tr>
            <td>1927</td>
            <td>Robert</td>
            <td>61671</td>
        </tr>
        <tr>
            <td>1926</td>
            <td>Robert</td>
            <td>61130</td>
        </tr>
        <tr>
            <td>1925</td>
            <td>Robert</td>
            <td>60897</td>
        </tr>
        <tr>
            <td>1924</td>
            <td>Robert</td>
            <td>60801</td>
        </tr>
        <tr>
            <td>1923</td>
            <td>John</td>
            <td>57469</td>
        </tr>
        <tr>
            <td>1922</td>
            <td>John</td>
            <td>57280</td>
        </tr>
        <tr>
            <td>1921</td>
            <td>John</td>
            <td>58215</td>
        </tr>
        <tr>
            <td>1920</td>
            <td>John</td>
            <td>56914</td>
        </tr>
    </tbody>
</table>




```python
%%nose
last_output = _

def test_output_type():
    assert str(type(last_output)) == "<class 'sql.run.ResultSet'>", \
    "Please ensure an SQL ResultSet is the output of the code cell." 

results = last_output.DataFrame()

def test_results():
    assert results.shape == (101, 3), \
    "The query should return 101 rows and three columns."
    assert set(results.columns.tolist()) == set(["year", "first_name", "num"]), \
    'The results should have three columns: "year", "first_name", and "num".'
    assert last_output.DataFrame().loc[0, 'year'] == 2020, \
    "The first year should be 2020. Did you sort so that the most recent years appear first?"
    assert last_output.DataFrame().loc[0, 'first_name'] == "Liam", \
    "In 2020, the name given to the most male babies was Liam."
    assert last_output.DataFrame().loc[0, 'num'] == 19659, \
    "In 2020, the name Liam was given to 19,659 babies."
```






    2/2 tests passed




## 8. The most years at number one
<p>Noah and Liam have ruled the roost in the last few years, but if we scroll down in the results, it looks like Michael and Jacob have also spent a good number of years as the top name! Which name has been number one for the largest number of years? Let's use a common table expression to find out. </p>


```sql
%%sql

-- Select first_name and a count of years it was the top name in the last task; alias as count_top_name
-- Use the code from the previous task as a common table expression
-- Group by first_name and order by count_top_name descending

WITH CTE AS (
SELECT baby_names.year, baby_names.first_name, baby_names.num 
FROM baby_names 
INNER JOIN ( 
    SELECT year, MAX(num) AS max_num
    FROM baby_names
    WHERE sex = 'M'
    GROUP BY year) AS m
ON baby_names.year = m.year AND baby_names.num = m.max_num
ORDER BY year DESC)

SELECT first_name, COUNT(year) AS count_top_name
FROM CTE
GROUP BY first_name
ORDER BY count_top_name DESC
```

     * postgresql:///names
    8 rows affected.





<table>
    <thead>
        <tr>
            <th>first_name</th>
            <th>count_top_name</th>
        </tr>
    </thead>
    <tbody>
        <tr>
            <td>Michael</td>
            <td>44</td>
        </tr>
        <tr>
            <td>Robert</td>
            <td>17</td>
        </tr>
        <tr>
            <td>Jacob</td>
            <td>14</td>
        </tr>
        <tr>
            <td>James</td>
            <td>13</td>
        </tr>
        <tr>
            <td>Noah</td>
            <td>4</td>
        </tr>
        <tr>
            <td>John</td>
            <td>4</td>
        </tr>
        <tr>
            <td>Liam</td>
            <td>4</td>
        </tr>
        <tr>
            <td>David</td>
            <td>1</td>
        </tr>
    </tbody>
</table>




```python
%%nose
last_output = _

def test_output_type():
    assert str(type(last_output)) == "<class 'sql.run.ResultSet'>", \
    "Please ensure an SQL ResultSet is the output of the code cell." 

results = last_output.DataFrame()

def test_results():
    assert results.shape == (8, 2), \
    "The query should return eight rows and two columns."
    assert set(results.columns.tolist()) == set(["first_name", "count_top_name"]), \
    'The results should have two columns: "first_name" and "count_top_name".'
    assert last_output.DataFrame().loc[0, 'first_name'] == 'Michael', \
    "The name that spent most years at number one should be Michael. Did you order from most to fewest years at the top?"
    assert last_output.DataFrame().loc[0, 'count_top_name'] == 44, \
    "Michael was the number one male name 44 times. It doesn't look like your results reflect this."
```






    2/2 tests passed



