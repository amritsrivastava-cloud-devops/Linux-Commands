# Linux Advanced
- ssh
- users and groups
- file permissions
- day to day life scenario (awk , grep , sed , find)
- networking commands

## Day to day life scenario (awk , grep , sed , find)

#### Grep (global regular expression print ) - If i want to check authentication failure from log file . 
-i ( case insensetive )
- grep -i "content to be found" filename 
- grep -i "authentication failure" app.log | tail -n 10

<img width="1010" height="529" alt="image" src="https://github.com/user-attachments/assets/0c423220-166a-44c6-b7d6-21404919b135" />
<img width="1021" height="354" alt="image" src="https://github.com/user-attachments/assets/9df028cc-dbfd-47e3-9530-85240f315763" />

- Now i want to get some particular infromation like i want to get hostname ip user ?

#### Awk is a tool 
- awk '/authentication failure/ {print}' app.log 
- awk '/content/ {print $column1,$column2......}' app.log | tail -n 20
- awk '/authentication failure/ { if ($1 == "Jul") && ($2 == "11") print $1,$2,$3,$12,$13,$14,$15}' app.log |tail -n 10

<img width="1026" height="248" alt="image" src="https://github.com/user-attachments/assets/f3fb75fa-dec7-450d-bdd5-24a9f81fc1d4" />
<img width="1020" height="410" alt="image" src="https://github.com/user-attachments/assets/b3b4d51e-f406-4782-ac12-7066b0d202a0" />

## sed is used to edit steam output (stream editor )
- sed  "s/user/username/g" authfail.log
<img width="691" height="192" alt="image" src="https://github.com/user-attachments/assets/65e25722-ad71-40cf-9f76-dcc43ecc0e49" />
- uniq data
- sed  "s/user/username/g" authfail.log | uniq
<img width="813" height="86" alt="image" src="https://github.com/user-attachments/assets/fc9a900a-edb1-449a-9d48-c3e466a9502d" />

## find is used to find file 
- find . -name app.log
<img width="718" height="72" alt="image" src="https://github.com/user-attachments/assets/99be3438-5c4d-4802-a748-90916e47fe82" />
