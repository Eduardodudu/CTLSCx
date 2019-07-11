
# CTLSC0x - Supply Chain Analytics




## Week2 - Unconstrained & Constrained Optimization

<Otpimization Packages>[link](http://www.is.uni-freiburg.de/resources/computational-economics/5_OptimizationR.pdf)

### Recitation

#### Unconstrained

Straigthforward exercises in modeling optimization without index vectors.

$$
\textbf{Equation 1:} \ \ Minimze \ \ y = x^2+2*x-3
$$

Since is an unconstrained quadratic optimization with a single variable, base R package optimizer can solve it


```r
f <- function(x){x^2+2*x-3}
result <- optimize(f,interval = c(-10,10), maximum = F)
print(result)
```

```
## $minimum
## [1] -1
## 
## $objective
## [1] -4
```

Let's check the result in the plot


```r
#Data created for plot, using mutate on the function
x <- seq(-10,10,1)
data <- data <- data.frame(x) %>%
        mutate(y = f(x))

#Ploting with a marker on the optimal solution
plot_ly(data, x = ~x, y = ~y, type = 'scatter', mode = 'lines') %>%
  add_markers(y = result$objective, x = result$minimum)
```

<!--html_preserve--><div id="htmlwidget-d0e715b8f5a5e402e810" style="width:672px;height:480px;" class="plotly html-widget"></div>
<script type="application/json" data-for="htmlwidget-d0e715b8f5a5e402e810">{"x":{"visdat":{"8242255a4a3":["function () ","plotlyVisDat"]},"cur_data":"8242255a4a3","attrs":{"8242255a4a3":{"x":{},"y":{},"mode":"lines","alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter"},"8242255a4a3.1":{"x":-1,"y":-4,"mode":"markers","alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"type":"scatter","inherit":true}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"xaxis":{"domain":[0,1],"automargin":true,"title":"x"},"yaxis":{"domain":[0,1],"automargin":true,"title":"y"},"hovermode":"closest","showlegend":true},"source":"A","config":{"showSendToCloud":false},"data":[{"x":[-10,-9,-8,-7,-6,-5,-4,-3,-2,-1,0,1,2,3,4,5,6,7,8,9,10],"y":[77,60,45,32,21,12,5,0,-3,-4,-3,0,5,12,21,32,45,60,77,96,117],"mode":"lines","type":"scatter","marker":{"color":"rgba(31,119,180,1)","line":{"color":"rgba(31,119,180,1)"}},"error_y":{"color":"rgba(31,119,180,1)"},"error_x":{"color":"rgba(31,119,180,1)"},"line":{"color":"rgba(31,119,180,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[-1],"y":[-4],"mode":"markers","type":"scatter","marker":{"color":"rgba(255,127,14,1)","line":{"color":"rgba(255,127,14,1)"}},"error_y":{"color":"rgba(255,127,14,1)"},"error_x":{"color":"rgba(255,127,14,1)"},"line":{"color":"rgba(255,127,14,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.2,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->



$$
\textbf{Equation 2:} \ \ Maximize \ \ z =-x^2+2*x-y^2
$$

In this equation there is 2 variables, for this case it can be used the base R package optim.



```r
f <- function(x,y){-x[1]^2+2*x[1]-x[2]^2}
result <- optim(c(1, 1), f)
print(result)
```

```
## $par
## [1] 4.996335e+54 2.684172e+55
## 
## $value
## [1] -7.454411e+110
## 
## $counts
## function gradient 
##      501       NA 
## 
## $convergence
## [1] 1
## 
## $message
## NULL
```


#### Constrained

##### Banner Chemicals


```r
data <- tibble(Type = c("Profit","Plant","Additive A", "Additive B"), High = c(80,1,3,1),
                   Supreme = c(200,1,2,3), Capacity = c(NA,110, 300, 280))

datatable(data)
```

<!--html_preserve--><div id="htmlwidget-ca24b3d0009e8a8f90a4" style="width:100%;height:auto;" class="datatables html-widget"></div>
<script type="application/json" data-for="htmlwidget-ca24b3d0009e8a8f90a4">{"x":{"filter":"none","data":[["1","2","3","4"],["Profit","Plant","Additive A","Additive B"],[80,1,3,1],[200,1,2,3],[null,110,300,280]],"container":"<table class=\"display\">\n  <thead>\n    <tr>\n      <th> <\/th>\n      <th>Type<\/th>\n      <th>High<\/th>\n      <th>Supreme<\/th>\n      <th>Capacity<\/th>\n    <\/tr>\n  <\/thead>\n<\/table>","options":{"columnDefs":[{"className":"dt-right","targets":[2,3,4]},{"orderable":false,"targets":0}],"order":[],"autoWidth":false,"orderClasses":false}},"evals":[],"jsHooks":[]}</script><!--/html_preserve-->



$$
\begin{align*}

\textbf{Maximize} & \ y = \sum_{i\in M}^{ }p_i\cdot x_i \\
\textbf{subject to}\\
    & \sum_{i\in M} x_i \le\ C \\
    & \sum_{i\in M} a_{i,j} \cdot x_i \le\ A_j, \ \ \forall j \in N \\
    & x_i \ge 0 \\
    \\
    
\textbf{Where} \\

& i = \text{Products in M} \\
& j = \text{Additives in N} \\
& p_i = \text{Profit margin for product i} \\
& C = \text{Plant capacity} \\
& A_j = \text{Additive j available} \\
& a_{i,j} = \text{Quantity of additive j required per barrel of product i} \\
& x_i = \text{Quantity of product i to produce} \\

\end{align*} 
$$


```r
rm(x)

n = 2 #Number of products High and Supreme
C = filter(data, Type == "Plant") %>% select(Capacity) %>% .$Capacity
a = as.matrix(filter(data, Type %in% c("Additive A","Additive B")) %>% select(High, Supreme))
A = filter(data, Type %in% c("Additive A","Additive B")) %>% select(Capacity) %>% .$Capacity
p = filter(data, Type == "Profit") %>% select(High, Supreme) %>% gather() %>% .$value


model <- MIPModel() %>%
  # Variable of profit
  add_variable(x[i], i = 1:n, type = "integer", lb = 0) %>%

  # minimize travel distance
  set_objective(sum_expr(p[i]*x[i], i = 1:n), "max") %>%

  # you cannot exceed the Plant Capacity
  add_constraint(sum_expr(x[i], i = 1:n) <= C) %>%
  
  #You cannot exceed aditivie capacity
  add_constraint(sum_expr(x[i] * a[j,i], i = 1:n) <= A[j], j = 1:n)


model$constraints
```

```
## [[1]]
## $lhs
## expression(x[1L] + x[2L])
## 
## $sense
## [1] "<="
## 
## $rhs
## expression(110)
## 
## attr(,"class")
## [1] "model_constraint"
## 
## [[2]]
## $lhs
## expression(c(High = 3) * x[1L] + c(Supreme = 2) * x[2L])
## 
## $sense
## [1] "<="
## 
## $rhs
## expression(300)
## 
## attr(,"class")
## [1] "model_constraint"
## 
## [[3]]
## $lhs
## expression(x[1L] * c(High = 1) + c(Supreme = 3) * x[2L])
## 
## $sense
## [1] "<="
## 
## $rhs
## expression(280)
## 
## attr(,"class")
## [1] "model_constraint"
```

```r
#Solve
result <- solve_model(model, with_ROI(solver = "glpk", verbose = TRUE))
```

```
## <SOLVER MSG>  ----
## GLPK Simplex Optimizer, v4.57
## 3 rows, 2 columns, 6 non-zeros
## *     0: obj =  -0.000000000e+00 inf =   0.000e+00 (2)
## *     2: obj =   1.900000000e+04 inf =   0.000e+00 (0)
## OPTIMAL LP SOLUTION FOUND
## GLPK Integer Optimizer, v4.57
## 3 rows, 2 columns, 6 non-zeros
## 2 integer variables, none of which are binary
## Integer optimization begins...
## +     2: mip =     not found yet <=              +inf        (1; 0)
## +     2: >>>>>   1.900000000e+04 <=   1.900000000e+04   0.0% (1; 0)
## +     2: mip =   1.900000000e+04 <=     tree is empty   0.0% (0; 1)
## INTEGER OPTIMAL SOLUTION FOUND
## <!SOLVER MSG> ----
```

```r
#Optimal Value
result$objective_value
```

```
## [1] 19000
```

```r
#result solution
result$solution
```

```
## x[1] x[2] 
##   25   85
```


