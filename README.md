Back End
	Models
		Entities
			Employe.cs
				Guid Id
				public required string Name
				public required string Email
				public string? Phone (Permite valores nullos)
				public decimal Salary

Back End
	Data
		using Microsoft.EntityFrameworkCore;
		namespace EmployeeAdminPortal.Data
  
		public class ApplicationDbContext: DbContext
		public ApplicatioDbContext(DbContextOptions<ApplicationDbContext> options): base(options)
		{
		}
		
		public DbSet<Employee> Employees {get; set;}

Back End
	appsettings.json
		"ConnectionStrings": {
		"DefaultConnection": "Server=localhost\\MSSQLSERVER01;Database=EmployeesDb; Trusted_connection=true;TrustedServerCertificate=true;"
		}

Back End
	Program.cs
		using EmployeeAdminPortal.Data;
		using Microsoft.EntityFramworkCore;

		builder.Services.AddDbContext<ApplicationDbContext>(options => 
		options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection")))

Tools
	NuGet Package Manager
		Package Manager Console
		PM> add-migration "initial migration"
		Crea una nueva clase Migration

		PM> update-database
		Dara un error!
		Hay que clicar sobre el programa principal, el que tiene la bola del mundo y poner
		el statement <InvariantGlobalization>false</InvariantGlobalization>

La base de datos ya esta creada

Back End
	Controllers
		EmployeesController.cs

		namespace EmployeeAdminPortal.Controllers
		{
		//localhost:xxx/api/employees
		[Route("api/[controller]")]
		[ApiController]
		public class EmployeesController: ControllerBase
		{
		private readonly ApplicationDbContext dbContext;
		public EmployeesController(ApplicationDbContext dbContext)
		{
		this.dbContext = dbContext;
		}
  		//Recuperar toda la información del db
		[HttpGet]
		public IActionResult GetAllEmployees()
		{
			var allEmployees = dbContext.Employees.ToList();
			
			return Ok(allEmployees);
		}
  
  		//Recuperar solo un elemento del db
    		[HttpGet]
      		[Route("{id:guid}")]
      		public IActionResult GetEmployeeById(Guid id) 
		{
  			var employee = dbContext.Employees.Find(id);
     			if (employee is null)
			{
   				return NotFound();
   			}
      			return Ok(employee);
		}
  
    		//Añadir información en el db
  		[HttpPost]
    		public IActionResult AddEmployee(AddEmployeeDto addEmployeeDto)
		{
  		var employeeEntity = new Employee() 
    		{
      		Name = addEmployeeDto.Name
		Email = addEmployeeDto.Email
  		Phone = addEmployeeDto.Phone
    		Salary = addEmployeeDto.Salary
    		};
  		dbContext.Employees.Add(employeeEntity);
    		dbContext.SaveChanges();

		return Ok(employeeEntity); 
		}

  		//Actualizar un elemento del dB
    		[HttpPut]
      		[Route("{id:guid}")]
      		public IActionResult UpdateEmployee(Guid id, UpdateEmployeeDto updateEmployeeDto)
		{
  			var employee = dbContext.Employees.Find(id);
     			if (employe is null)
			{
   				return NotFound();
   			}
      			employee.Name = updateEmployeeDto.Name;
	 		employee.Email = updateEmployeeDto.Email;
  			employee.Phone = updateEmployeeDto.Phone;
 			employee.Salary = updateEmployeeDto.Salary;

    			dbContext.SaveChanges();
       			return Ok(employee);
  		}

    		//Eliminar un elemento del dB
      		[HttpDelete]
		[Route("{id:guid}")]
  		public IActionResult DeleteEmployee(Guid id)
  		{
    		var employee = dbContext.Employees.Find(id);
      		if (employee is null)
		{
  			return NotFound();
  		}
    		dbContext.Employees.Remove(employee);
      		dbContext.SaveChanges();
		return Ok();
    		}
Back End
	Models
 		(Copiamos los mismos parametros que la entidad, porque usamos esta clase
   		como un auxiliar para mover la información)
   		AddEmployeeDto.cs
       		namespace EmployeeAdminPortal.Models
	 	public class AddEmployeeDto
   		{
     				public required string Name
				public required string Email
				public string? Phone (Permite valores nullos)
				public decimal Salary
     		}
Back End
	Models
 		(Copiamos los mismos parametros que la entidad, porque usamos esta clase
   		para actualizar la información del dB)
   		UpdateEmployeeDto.cs
       		namespace EmployeeAdminPortal.Models
	 	public class UpdateEmployeeDto
   		{
     				public required string Name
				public required string Email
				public string? Phone (Permite valores nullos)
				public decimal Salary
     		}
     		
