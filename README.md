Project: Revenue Insights in Hospitality

Procedure:

step1:
Import data by clicking on "get data" from Home tab then import the required data folder.
click on transform data, 
duplicate input_files multiple times it will use for the below tables.
Step2:
rename duplicate table by keeping required columns accordingly.
1.Dim_date   (delete date_type column)
2.dim_hotels
3.dim_rooms 
4.fact_aggregated_bookings
5.fact_bookings

Then, click on "close and apply"

DataModeling

Step3:
property id connected with property id (It will automatically connect)
step4:
connect dim_date to check_in_date-(fact_bookings table)  and room_id to dim_rooms and room_category (fact_aggregated_bookings table)

Data view
step5:
create new column with formule

daytype=

var wkd = weekday(dimdate[date])

if(wkd>5,"weekend","weekday")

as per our data both Friday and Saturday as weekends

step6:
Created new caluclated table Key Measures

Revenue= SUM (factbookings[revenue-realized])

Revenue	To get the total revenue_realized	

Revenue = SUM(fact_bookings[revenue_realized])

step7:
Total Bookings	To get the total number of bookings happened	

Total Bookings = COUNT(fact_bookings[booking_id])

Total Capacity	To get the total capacity of rooms present in hotels	

Total Capacity = SUM(fact_aggregated_bookings[capacity])

Total Succesful Bookings To get the total succesful bookings happened for all hotels	

Total Succesful Bookings = SUM(fact_aggregated_bookings[successful_bookings])


Occupancy %	"Occupancy means total successful bookings happened to the 
total rooms available(capacity)"	

Occupancy % = DIVIDE([Total Succesful Bookings],[Total Capacity],0)

Average Rating	Get the average ratings given by the customers	

Average Rating = AVERAGE(fact_bookings[ratings_given])

No of days	"To get the total number of days present in the data.
In our case, we have data from May to July. So 92 days.

No of days = DATEDIFF(MIN(dim_date[date]),MAX(dim_date[date]),DAY) +1


Total cancelled bookings To get the"Cancelled" bookings out of all Total bookings happened	

Total cancelled bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="Cancelled")

Cancellation %	"calculating the cancellaton percentage.

Cancellation % = DIVIDE([Total cancelled bookings],[Total Bookings])

Total Checked Out To get the successful 'Checked out' bookings out of all Total bookings happened	

Total Checked Out = CALCULATE([Total Bookings],fact_bookings[booking_status]="Checked Out")

Total no show bookings	"To get the""No Show"" bookings out of all Total bookings happened 

(""No show"" means those customers who neither cancelled nor attend to their booked rooms)"	

Total no show bookings = CALCULATE([Total Bookings],fact_bookings[booking_status]="No Show")

No Show rate %	calculating the no show percentage.	

No Show rate % = DIVIDE([Total no show bookings],[Total Bookings])

Booking % by Platform	"To show the percentage contribution of each booking platform for bookings in hotels.

We have booking platforms like makeyourtrip, logtrip, tripster etc)"	

Booking % by Platform = DIVIDE([Total Bookings],
 CALCULATE([Total Bookings], 
 ALL(fact_bookings[booking_platform])
  ))*100"

Booking % by Room class	"To show the percentage contribution of each room class
over total rooms booked.

We have room classes like Standard, Elite, Premium, Presidential."

Booking % by Room class = DIVIDE([Total Bookings],
 CALCULATE([Total Bookings], 
 ALL(dim_rooms[room_class])
  ))*100"

ADR Calculate the ADR(Average Daily rate)

It is the ratio of revenue to the total rooms booked/sold. 
It is the measure of the average paid for rooms sold in a given time period"	

ADR = DIVIDE( [Revenue], [Total Bookings],0)


Realisation %	"calculate  the realisation percentage.

It is nothing but the succesful ""checked out"" percentage over all bookings happened.


Realisation % = 1- ([Cancellation %]+[No Show rate %])

RevPAR	"Calculate the RevPAR(Revenue Per Available Room)

RevPAR represents the revenue generated per available room, whether or not they are occupied. RevPAR helps hotels measure their revenue generating performance to accurately price rooms. RevPAR can help hotels measure themselves against other properties or brands."	


RevPAR = DIVIDE([Revenue],[Total Capacity])


DBRN	"calculate DBRN(Daily Booked Room Nights)

This metrics tells on average how many rooms are booked for a day considering a time period

DBRN = DIVIDE([Total Bookings], [No of days])

DSRN 	"calculate DSRN(Daily Sellable Room Nights)

This metrics tells on average how many rooms are ready to sell for a day considering a time period

DSRN = DIVIDE([Total Capacity], [No of days])

DURN	"calculate DURN(Daily Utilized Room Nights)

This metric tells on average how many rooms are succesfully utilized by customers for a day considering a time period


DURN = DIVIDE([Total Checked Out],[No of days])

Revenue WoW change %	"To get the revenue change percentage week over week.

Here, 
revcw  for current week
revpw for previous week


"	"Revenue WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Revenue],dim_date[wn]= selv)
var revpw =  CALCULATE([Revenue],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"
Occupancy WoW change %	"To get the occupancy change percentage week over week.

Here, 
revcw  for current week
revpw for previous week"	"Occupancy WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Occupancy %],dim_date[wn]= selv)
var revpw =  CALCULATE([Occupancy %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"
ADR WoW change %	"To get the ADR(Average Daily rate) change percentage week over week.

Here, 
revcw  for current week
revpw for previous week"	"ADR WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([ADR],dim_date[wn]= selv)
var revpw =  CALCULATE([ADR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"
Revpar WoW change %	"To get the RevPar(Revenue Per Available Room) change percentage week over week.

Here, 
revcw  for current week
revpw for previous week"	"Revpar WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([RevPAR],dim_date[wn]= selv)
var revpw =  CALCULATE([RevPAR],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"
Realisation WoW change %	"To get the Realisation change percentage week over week.

Here, 
revcw  for current week
revpw for previous week"	"Realisation WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([Realisation %],dim_date[wn]= selv)
var revpw =  CALCULATE([Realisation %],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"
DSRN WoW change %	"To get the DSRN(Daily Sellable Room Nights) change percentage week over week.

Here, 
revcw  for current week
revpw for previous week"	"DSRN WoW change % = 
Var selv = IF(HASONEFILTER(dim_date[wn]),SELECTEDVALUE(dim_date[wn]),MAX(dim_date[wn]))
var revcw = CALCULATE([DSRN],dim_date[wn]= selv)
var revpw =  CALCULATE([DSRN],FILTER(ALL(dim_date),dim_date[wn]= selv-1))

return


DIVIDE(revcw,revpw,0)-1"
		


Dashboard

step8:
create table visual by drag and drop from visuals into report view.
step9:
add property_id, property_name, revenue, Realisation %, RevPAR, DBRN, DSRN, DURN and ADR.

step10:
convert occupancy, Realisation, rating column values into %

step11:
Make use conditional formation to add adding databars with colors for revenure  and rating in the table.

create slicers for city and room_class columns with dropdown option.


add slicer for week_no with Tile option. 

For revenue column revenue in the card properties change values type as decimals, colours and title = Revenue
Add a card in the revenue card by renaming for wow change with dot with small size and remove category lable to hide dot, in the visuals cell elements we can add down (for low value) and up (high value) arrows by condition

step12:
create tooltip for revenue card
step13:
new page and revenue with line chart x-axis week number category also
step14:
datalabes allow use tooltip
step15:
enable tooltips for revenue card in page1
step16:
Realization% and ADR by platform with column chart
step17:
donut chart revenue% and category

trend by Key metrics

"trend by week tooptip with revenue trend by week

RevPAR tooltip  revpar by week number and daytpe

DSRN tooltip DSRN by week no

Ocupancy tooltip with Occupancy and weekno and daytype

ADR tooltip ADR by week no 

Realz% tooltip by realisation% by week no and daytype

step18:
We can add Title and logos on the top using selection pane
step19:
then add other sourcers to navigate like youtube,linkdin and Q&A using style under actions web url and provide required URL

step20:
Finally we can publish the tailored Dashboard into Power BI services under myworkspace.



















