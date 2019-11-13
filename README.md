# Making a simple CRUD with JavaWeb

In this tutorial, you will find a step-by-step on what you should do to make a simple **CRUD with JavaWeb**. Follow each step and in the end you will probably be ready to Unip's ALPOO Exam.
The source code is open, but everything you need will be in the **README**.

## Preparing Ambient

First of all, you gonna need Netbeans and JDK installed in your machine. Links for both:
**[Netbeans Download](https://netbeans.org/downloads/8.0.2/)**

 - Download the biggest one, that has almost everything checked.

**[JDK Download](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html)**

 - Get the version that matches your computer config. I mean windows x64 ou x86, Linux or MacOS. 

Also need some SQL shenanigans

[**SQL Server Express**](https://www.microsoft.com/pt-br/sql-server/sql-server-downloads)
 - A simple next-next-next installation
 - In the end click to install the SSMS. You are going to need it.

**[SQL Serve Driver for Java](https://docs.microsoft.com/pt-br/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?view=sql-server-ver15)**

- You will need this one later in the tutorial

## Starting with Java Web

For now, let's forget about coding. I know, the Exam is all about coding as fast as possible, but first, you gonna need to configure some things to your project, else you will fail the Exam and go to **EXAME** and we don't want that. 

### The SQL Script

If you are doing this in your PC, probably the SQL Server Management Studio is not configured. Let's configure it:

 1. Configure the *sa* user and habilitate SQL Server login
 - Open SQL Server Management Studio and click in connect
 - Now, in the left window, expand Security > Logons, find *sa*, right click it and go to properties.
 - Change the password (do not forget)
 - Go to status and conced and habilitate both radio buttons.
 - After that, close the sa properties and right click the instance of SQL (the first item in the left menu)
 - Go to its properties and security. In the Autentication Server, select the second option.

 2. Open the door 1433 (Netbeans will need this open)
 - Open the SQL Server Configuration Manager
 - Expand the  SQL Server Network Configuration
 - Select the protocols (this will open some options in the right)
 - Habilitate the TCP/IP and right click it and open its properties
 - In the Ip Address tab, scroll down until you find IPAII
 - Put in the TCP Door: 1433
 - Reset the SQL Serve Express Services. Open the windows services and find it

3. Finally the script!
For this example, we will make a CRUD of a car. So create a new SQL query and put this script:

    create database ALPOO
    go
    
    use ALPOO
    go
    
    create table Carros
    (
    id int identity (1,1)
    fabricante varchar (50),
    modelo varchar (50),
    ano varchar (4),
    valor money
    )
    go

That's it for SQL Server. In the Exam, you only need to do the part 3.

### Creating the Netbeans project

 1. Create a new project. Java Web > Web Application

 #### Hibernate configuration
 
 1.  Open the services tab (**ctrl+5** or **Windows > Services**)
 2. Right click the "Database" and new connection.
 3. Remember the SQL Driver? Find it and select in this new window (the name that will be displayed is **Microsoft SQL Server 2005**)
 4. Click next and put put this:
 - **Host:** localhost
 - **Door:** 1433
 - **Database:** ALPOO
 - **Instance:** It is the text when you click to connect the SQL Server, in my case is FELIPE-NOTEBOOK\SQLEXPRESS
 - **User:** sa
 - **Password:** sa's password
5. CLICK TEST CONNECTION. Sorry for the caps, but it is a **must do** thing.
6. Click next and select dbo.
7. Conclud the process. 

Now let's see if the Hibernate managed to find your connection

1. Go back to your project tab (ctrl+1)
2. Right click **Source Code Packages**
3. Go to 'others' and find the hibernate dir
4. Select the first one. Click in next and select the connection you had just created 
5. After clicking in 'finish', open the file and expand the first drop down menu. There should be the connection string configurations there. If it's not there, delete this project and start over the netbeans part (frustrating, I know).

Ok, our Hibernate is good to go let's do the last part of it.

1. Create the package **Modelo** and inside it, create a new item
2. Click to add > others > persistence > Database entity class
3. Select your connection string and add your **Carros** table
4. Click next and change the class name to **Carro**
5. Click next one more time and in the collection type, select *java.util.list*
6. Finish
7. Now open again the hibernate config file and go to mapping
8. There you will add this class you just created, the **Carro**

Now, you should be good to go with the project.

### Creating the DAL package

After all that hibernate thing, we can start coding! After one more part... It's a quick one, I swear.

1. As the title says, create the DAL package.
2. Let's create the class '*Conexao*'. Same process, Add > others > hibernate > hibernateUtil.java and name it as Conexao.

Congrats! You have probably 2/10 in the Exam. :(
BUT **now we can code!** 

Let's make the **CarroDAO** class

 1. It's a simple java class here, so just add one.
 2. Insert this code inside of the class { }

   public String mensagem;
    Conexao conexao = new Conexao();
    Session session = Conexao.getSessionFactory().openSession(); `

    public void CadastrarCarro(Carros carro) {

        this.mensagem = "";

        try {
            session.beginTransaction();
            session.save(carro);
            session.getTransaction().commit();
            session.close();
            this.mensagem = "Cadastro efetuado";
        } catch (HibernateException e) {
            this.mensagem = e.getMessage();
        }
    }

    public Carros PesquisarCarro(Carros carro) {
        this.mensagem = "";
        Query query = session.createQuery("from Carros c where c.id = :id");
        query.setParameter("id", carro.getId());

        if (query.list().size() < 1) {
            this.mensagem = "Não existe este ID";
        } else {
            carro = (Carros) query.list().get(0);
        }

        return carro;
    }

Since you are here to learn, try reading the first method and make the delete and update one. Believe me, it's not hard at all. If you just can't do it, clone the source code or just go where the DAO class is and find out the boring way. 

That's it for the DAL package. 

### Modelo Package classes

If you are following step by step, this package should be created already. So I will not even bother telling you to create one.
This package is were all your logic belongs, here we validate, build and control were things go and what order they should go. 

Let's make the **Validacao** class. 
1. Create a new java class for it.
2. Put this code inside. 


public String mensagem;
    public int id;

    public void ValidaCarro(Carros carro) {
        this.mensagem = "";
        if (carro.getFabricante().length() < 3) 
            this.mensagem += "Fabricante com menos de 3 caracteres \n";         
    }
    
    public void ValidarId(String id){
        this.mensagem = "";
        try {
            this.id = Integer.parseInt((id));
        } catch (Exception e) {
            this.mensagem = "Id Inválido";
        }
    }

Put as many validations as you pleased

Let's make the **Controle** class
1. Create a new java class
2. Put this code:

public String mensagem;
    
    public void CadastrarCarro(Carros carro){
        this.mensagem = "";
        Validacao validacao = new Validacao();
        validacao.ValidaCarro(carro);
        if(validacao.mensagem.equals("")){
            CarroDAO carroDAO = new CarroDAO();
            carroDAO.CadastrarCarro(carro);
            this.mensagem = carroDAO.mensagem;
        }
        else
            this.mensagem = validacao.mensagem;
    }
As you can see, it is quite simple. First you validate your data and call the DAO method. 
Just like the DAO part, Here you will need to create a method for each CRUD letter. We already have the C, so sit down, learn how to code and make the RUD methods. It's literally the same code. 

After that reality shock, we have ended the **Modelo** package. 

### ManagedBeans package

Some sweet placeholder.

## Java Web, a bad way to web dev

Ready for it?? Read this second sweet placeholder.