# iXperience - Full Stack

Welcome to iXperience Full Stack 2019!

[[toc]]

## Charts.js Library Setup

### Installation

Enter in VS Code Terminal
```bash
npm install ng2-charts
npm install chart.js
```
## Charts.js Library Usage

### Import into AppModule

Import Chart.js as a service
```ts
import { ChartsModule } from 'ng2-charts';
 
@NgModule({
  imports: [
    ...,
    ChartsModule
  ],
  ...
})
export class AppModule { }
```

### Display Graph Component

Add Charts.js graph component in the component.html document
```html
<div>
  <div style="display: block">
    <canvas baseChart
            [datasets]="barChartData"
            [labels]="barChartLabels"
            [options]="barChartOptions"
            [legend]="barChartLegend"
            [chartType]="barChartType">
    </canvas>
  </div>
</div>
```

### Declare the Graph Object Variables 

Declare the graph object variables in the component.ts file, graph data is contained within barChartData
```ts

// ...
 
constructor() { }

barChartOptions = {
  scaleShowVerticalLines: false,
  responsive: true
};

barChartLabels = ['2006', '2007', '2008', '2009', '2010', '2011', '2012'];
barChartType = 'bar';
barChartLegend = true;
barChartData = [
  {data: [65, 59, 80, 81, 56, 55, 40], label: 'Series A'},
  {data: [28, 48, 40, 19, 86, 27, 90], label: 'Series B'}
];

ngOnInit() {
}

```
## Create Dashboard Graphs with Custom Data

### Create Service to Fetch Graph Data 

From the project file directory create a service graph-data

```bash
ng generate service services/admin-panel/graph-data
```
Import graph-data service into AppModule

```ts
import { GraphDataService } from '../app/services/admin-panel/graph-data.service';
 
@NgModule({
  providers: [
    ...,
    GraphDataService
  ],
  ...
})
export class AppModule { }
```

Import graph-data service into the component.ts and define a instance of the service in the constructor (dependency injection)

```ts
import { GraphDataService } from '../services/admin-panel/graph-data.service';
 
constructor(private graphDataService: GraphDataService) { }

```

### Setup Data for Total Monthly Rental Income 

Implement a data fetching method using best practices. To do so create a graph-data model.

```bash
ng generate class models/graph-data 
```

In the graph-data.ts class file add the following model structure. Default data is added in this example.  

```ts

export class GraphData {
    xAxis: Array<string> = ['Jan', 'feb', 'Mar'];
    data: Array<number> = [100, 100, 100];
    label: string = "monthly percentage";
}

```

In the graph data service, create an object containing the data for total monthly rental income.

```ts

import { GraphData } from '../../models/graph-data';

//....

export class GraphDataService {

  constructor() { }

  public getTotalMonthlyRental() {
    let rentalIncome = new GraphData(); // ensure data is in the form of GraphData model
    rentalIncome.xAxis =
    [
      'Jan', 
      'Feb', 
      'Mar', 
      'Apr', 
      'May', 
      'Jun', 
      'Jul', 
      'Aug', 
      'Sep', 
      'Oct', 
      'Nov', 
      'Dec'
    ];
    rentalIncome.data = 
    [
      152, 
      128, 
      103, 
      112, 
      113, 
      125, 
      142, 
      132, 
      135, 
      138, 
      145, 
      167
    ];
    rentalIncome.label = "Monthly Rental Income";
    return rentalIncome;
  }

}

```

### Get Total Monthly Rental Income Data 

Create graph variables necessary for the new graph in the component.ts file. In the ngOnInit() function (runs when the component is initialized), read the data from the GraphDataService.

```ts

import { GraphDataService } from '../services/admin-panel/graph-data.service';

rentalGraphOptions = {
  scaleShowVerticalLines: false,
  responsive: true,
};
rentalGraphLabels: Array<string>;
rentalGraphType = 'bar';
rentalGraphLegend = true;
rentalGraphData: any;

ngOnInit() {
  let rentalData = this.graphDataService.getTotalMonthlyRental();
  this.rentalGraphLabels = rentalData.xAxis;
  this.rentalGraphData = [{
    data: rentalData.data,
    label: rentalData.label,
  }];
}

```

Update the component.html file to include the new variable names created in the component.ts file.

```html
<div>
  <div style="display: block">
    <canvas baseChart
            [datasets]="rentalGraphData"
            [labels]="rentalGraphLabels"
            [options]="rentalGraphOptions"
            [legend]="rentalGraphLegend"
            [chartType]="rentalGraphType">
    </canvas>
  </div>
</div>
```
## Create a Pie Chart

### Setup Data for Pie Chart

In the GraphDataService create a function to get percentage data of guest booking lengths

```ts

public getLengthOfGuestBookings() {
  let guestBookings = new GraphData();
  guestBookings.xAxis =
  [
    'Less than 3 days', 
    '3 - 7 days', 
    '7-14 days', 
    'More than 14 days'
  ];
  guestBookings.data = 
  [
    25, 
    31, 
    28, 
    16
  ];
  guestBookings.label = "Length of Guest Bookings";
  return guestBookings;
}

```

### Create Model for Graphs

To follow best practices, create a model for graph objects. This allows for graph objects to be used more easily through out an application.

```bash
ng generate class models/graph 
```

In the graph.ts class file, add the following model structure. Default data is added in this example.

```ts

export class Graph {
    options: Options = new Options();
    title: string = 'Graph'
    labels: Array<string> = ['Jan', 'feb', 'Mar'];
    type: string = 'bar';
    legend: Boolean = true;
    data: Array<Data>;
}

export class Options {
    scaleShowVerticalLines: Boolean = false;
    responsive: Boolean = true;
}

export class Data {
    data: Array<number> = [65, 59, 80];
    label: string = 'Series A';
}

```

### Create a Method to Create and Load Data for Graphs

Create a function in the component.ts file to more easily initialize graphs and load data

```ts

import { Graph } from '../models/graph';

guestBookingLength: Graph = new Graph();

constructor(private graphDataService: GraphDataService) { 
}

ngOnInit() {
  this.guestBookingLength = this.updateGraph(this.graphDataService.getLengthOfGuestBookings(), 'pie', 'Guest Booking Length');
}

updateGraph(data, type, title){
  let graph = new Graph();
  graph.data = [];
  graph.data.push(data);
  graph.labels = data.xAxis;
  graph.type = type;
  graph.title = title;
  return graph;
}

```

Update component.html file to add new graph and set data binding. Add a heading/title to the graph 

```html
<div>
  <div class="chart-container">
    <strong class="graph-title">{{guestBookingLength.title}}</strong>
    <canvas baseChart
            [datasets]="guestBookingLength.data"
            [labels]="guestBookingLength.labels"
            [options]="guestBookingLength.options"
            [legend]="guestBookingLength.legend"
            [chartType]="guestBookingLength.type">
    </canvas>
  </div>
</div>
```

Update the component.css file to add additional styling.

```css
.chart-container {
    width: 100%;
    display: block;
    text-align: center;
}

.graph-title {
    color: purple;
}
```

## Create an Interactive Graph

### Randomly Generate Graph Data

In the GraphDataService, create functions to randomly generate data. The example shows data for guests making bookings in a year.

Generate a model to follow best practices and better structure the code.

```bash
ng generate class models/guest-booking-length 
```

In the class guest-booking-length.ts file, add the following structure.

```ts

export class GuestBookingLength {
  lessThan3: Array<number>;
  between3and7: Array<number>;
  between7and14: Array<number>;
  above14: Array<number>;
}

```

Generate the random guest rental data in the GraphDataService.

```ts

import { GuestBookingLength } from '../../models/guest-booking-length';

getRandomAnnualBookingLength() {
  let bookingsPerMonth = 20;
  let months = 12;

  let monthlyBookings = new GuestBookingLength();

  monthlyBookings.lessThan3 = [];
  monthlyBookings.between3and7 = [];
  monthlyBookings.between7and14 = [];
  monthlyBookings.above14 = [];

  for (let i = 0; i < months; i++) {
    monthlyBookings = this.addMonth(monthlyBookings);
    for (let j = 0; j < bookingsPerMonth; j++) {
      monthlyBookings = this.updateBookings(monthlyBookings, i); 
    }
  }
  return monthlyBookings;  
}

addMonth(bookings) {
  bookings.lessThan3.push(0);
  bookings.between3and7.push(0);
  bookings.between7and14.push(0);
  bookings.above14.push(0);
  return bookings;
}

updateBookings(bookings, month) {
  let bookingLength = Math.random() * 20;
  if (bookingLength < 3) {
    bookings.lessThan3[month] += 1;
  }
  else if (bookingLength >= 3 && bookingLength < 7) {
    bookings.between3and7[month] += 1;
  }
  else if (bookingLength >= 7 && bookingLength < 14) {
    bookings.between7and14[month] += 1;
  }
  else if (bookingLength >= 14) {
    bookings.above14[month] += 1;
  }
  return bookings;
}

```

### Extract Randomly Generated Data into a Pie Chart Format

Create a function to extract data in the form of a pie chart from the random monthly guest rental data. This is contained within the GraphDataService.

```ts

getRandomMonthlyBookingPieChart() {
  let monthlyBookings = this.getRandomAnnualBookingLength();
  let fieldNames = Object.keys(monthlyBookings);
  let data = [];

  fieldNames.forEach(fieldName => {
    let totalBookings = 0;
    for (let i = 0; i < monthlyBookings[fieldName].length; i++) {
      totalBookings += monthlyBookings[fieldName][i];
    }
    data.push(totalBookings);
  })

  let guestBookings = new GraphData();
  guestBookings.xAxis = fieldNames;
  guestBookings.data = data;
  guestBookings.label = "Length of Guest Bookings";
  return guestBookings;
}

```

### Display Pie Chart (Constructed from Random Data)

Create graph object in the component.ts file 

```ts

randomBookingLength: Graph = new Graph();

ngOnInit() {
  this.randomBookingLength = this.updateGraph(this.graphDataService.getRandomMonthlyBookingPieChart(), 'pie', 'Random Guest Booking Length');
}

updateGraph(data, type, title){
  let graph = new Graph();
  graph.data = [];
  graph.data.push(data);
  graph.labels = data.xAxis;
  graph.type = type;
  graph.title = title;
  return graph;
}

```

Update the component.html file

```html

<div>
  <div class="chart-container">
    <strong class="graph-title">{{randomBookingLength.title}}</strong>
    <canvas baseChart
            [datasets]="randomBookingLength.data"
            [labels]="randomBookingLength.labels"
            [options]="randomBookingLength.options"
            [legend]="randomBookingLength.legend"
            [chartType]="randomBookingLength.type">
    </canvas>
  </div>
</div>

```

### Add Click Event and Debug 

Add click event in the component.html as seen in the documentation

```html

<div>
  <div class="chart-container">
    <strong class="graph-title">{{randomBookingLength.title}}</strong>
    <canvas baseChart
            [datasets]="randomBookingLength.data"
            [labels]="randomBookingLength.labels"
            [options]="randomBookingLength.options"
            [legend]="randomBookingLength.legend"
            [chartType]="randomBookingLength.type"
            (chartClick)="chartClicked($event)">
    </canvas>
  </div>
</div>

```

Add click function in the component.ts file and console.log() the output to determine possible integration parameters.

```ts

chartClicked({ event, active }: { event: MouseEvent, active: any[] }): void {
  console.log("event:", event);
  console.log("active:", active);
}11

```

From the output of active, the data id/label can be accessed by

```ts

// Data ID
let index = (active[0]._index);
// Data Label
let field = this.randomBookingLength.labels[index];

```

### Create Component to Visualize Sub Graph 

Create component to visualize sub-graph. the sub-graph contains the number of bookings made with in a certain category (length of booking) for each month in a year.

```bash
ng generate component components/sub-graph 
```

### Create a Method to Extract Sub Graph Data 

Create a function in the GraphDataService to extract this data based on the selected category. This category will be selected when clicking on the graph.

```ts

getRandomAnnualBooking(fieldName) {
  let monthlyBookings = this.getRandomAnnualBookingLength();

  let guestBookings = new GraphData();
  guestBookings.xAxis = [
    'Jan', 
    'Feb', 
    'Mar', 
    'Apr', 
    'May', 
    'Jun', 
    'Jul', 
    'Aug', 
    'Sep', 
    'Oct', 
    'Nov', 
    'Dec'
  ];
  guestBookings.data = monthlyBookings[fieldName];
  guestBookings.label = "Monthly Guest Bookings of Type " + fieldName;
  return guestBookings;
} 

```

### Create Routing from Dashboard to Sub Graph Component

In the dashboard component.ts

```ts

import { Router } from '@angular/router';

constructor(
  private graphDataService: GraphDataService,
  private router: Router
  ) { 
}

chartClicked({ event, active }: { event: MouseEvent, active: any[] }): void {
  let index = (active[0]._index);
  let field = this.randomBookingLength.labels[index];
  console.log(index);
  console.log(field);
  this.router.navigate(["sub-graph/" + field]);
}  

```

In the app-routing.module.ts

```ts

const appRoutes: Routes = [
  { path: '', component: AdminDashboardComponent },
  { path: 'sub-graph/:field', component: SubGraphComponent }
];

```

In the sub-graph.component.ts

```ts

import { ActivatedRoute, Params } from "@angular/router";

field: string;

constructor(
  private graphDataService: GraphDataService,
  private route: ActivatedRoute
  ) { }

ngOnInit() {
  this.route.params.forEach((params: Params) => {
    this.field = params['field'];
  });
}

```

### Load the Sub Graph Data in the Sub Graph Component

In the sub-graph.component.ts

```ts

import { GraphDataService } from '../../services/admin-panel/graph-data.service';
import { Graph } from '../../models/graph';

guestMonthlyBooking: Graph = new Graph();

ngOnInit() {
  this.route.params.forEach((params: Params) => {
    this.field = params['field'];
  });
  this.guestMonthlyBooking = this.updateGraph(this.graphDataService.getRandomAnnualBooking(this.field), 'bar', ('Random Guest Booking'));
}

updateGraph(data, type, title){
  let graph = new Graph();
  graph.data = [];
  graph.data.push(data);
  graph.labels = data.xAxis;
  graph.type = type;
  graph.title = title;
  return graph;
}

```

In the sub-graph.component.html

```html

<div>
  <div class="chart-container">
    <strong class="graph-title">{{guestMonthlyBooking.title}}</strong>
    <canvas baseChart
        [datasets]="guestMonthlyBooking.data"
        [labels]="guestMonthlyBooking.labels"
        [options]="guestMonthlyBooking.options"
        [legend]="guestMonthlyBooking.legend"
        [chartType]="guestMonthlyBooking.type">
    </canvas>
  </div>
</div>

```

In the sub-graph.component.css

```css

.chart-container {
  width: 100%;
  display: block;
  text-align: center;
}

.graph-title {
  color: purple;
}

```
