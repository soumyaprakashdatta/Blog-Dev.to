**init function in golang**



init() in golang are special functions that run before other code blocks in packages. init functions are run -    

1. All the imported packages are initialized. 

2. After all variable declared in the package are initialized

   

Signature of init function is -   `func init(){ //your code goes here }`



Lets checkout some code example

**Scenario 1**

![init_simple](E:\workspace\myspace\dev_blog\Blog\golang\init_simple.PNG)



In this example as you can see, first variable a is initialized, for that initA function was called, there was no other packages imported, so no other package initialization was required, then init() function was called automatically and finally main() function was invoked.



**Scenario 2**

Now lets throw in some packages in the mix and see what's the call order and behavior of init funciton.

Modified folder structure looks like 

![new_project_structure](E:\workspace\myspace\dev_blog\Blog\golang\new_project_structure.PNG)



Modified code looks like - 

<img src="E:\workspace\myspace\dev_blog\Blog\golang\out1.PNG" alt="out1"  />



Now when we run the main function we see the following output on the right panel, lets  analyze what's going on 

1. In main.go first import statement is importing depA package.
2. In depA package there are two files, in golang they load files in a package in lexical filename order (alphabetical order), hence first a1.go was loaded and then a2.go
3. a1.go and a2.go both are importing package depB, hence first debB will be loaded and init function is called for the single file present in that package
4. Inside a file init invocation is in the order of declaration, hence first we see the first init declaration from b.go is invoked and then the second one.
5. init is called once per package, hence although both a1.go and a2.go are importing it, init from depB is called only once.
6. So in our example, order of init call was b.go init 1 -> b.go init 2 -> a1.go init 1 -> a1.go init 2 -> a2.go init 1 -> a2.go init2 -> main.go 
7. Inside main order of execution is : load necessary packages first, then initialize globals, then invoke init, finally invoke main function.   





**Couple of points to note at this point ** 

1. Init functions are called only once when the package is being imported.
2. You can declare more than one init functions in a file/package
3. You can not call init function explicitly , for example, if we call init() inside main, it will not compile and report that init is undefined.





**Where init functions are generally used **

1.  variable initialization can be put inside init 

2.  you can check for different conditions and initialize initial package state from init

3.  if you want to run some one time computation, eg. lets say you want to set random number seed for a package,  you can do it inside init function

4. register functionality, which goes hand in hand with "importing for side effect". Eg. registering database driver. Let's look at one small example from [https://github.com/go-sql-driver/mysql]:

   ```
   import "database/sql"
   import _ "github.com/go-sql-driver/mysql"
   
   db, err := sql.Open("mysql", "user:password@/dbname")
   ```

   In this section "github.com/go-sql-driver/mysql" package is imported for side-effect and not being used explicitly, generally we can assume some init() function is being called inside the package and registering necessary driver. In this case, you can find the init call in driver.go [https://github.com/go-sql-driver/mysql/blob/master/driver.go]:

   ```
   func init() {
   	sql.Register("mysql", &MySQLDriver{})
   }
   ```

   



**Drawback for init function**

1. It reduced readability of code
2. Creates problem in testing
3. One important drawback of using init function is the unpredictability of the init call order. In our example, if we just rename a1.go file to a3.go then init call orders will change as a2.go will be loaded first, then a3.go.



p.s. - this is my first blog post , let me know how I can improve it.

you can reach me at - @\__spd__ at twitter or email me at dattasoumyaprakash@gmail.com 





![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/82pa6kxhbitsy8qoiol7.PNG)
![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/zf38lr4p81byb9xmcrzf.PNG)
![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/u36duidyk3wmvrjxb0qt.PNG)
![Alt Text](https://thepracticaldev.s3.amazonaws.com/i/o871pbvqq5vyq9nnrrv2.PNG)
)