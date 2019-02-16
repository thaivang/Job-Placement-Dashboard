## Job Placement Dashboard

Two week live project at The Tech Academy

On a team of 5 people, we were tasks with creating a web application that a student will register and then it would populate fields where stuents will have to fill out base on the following fields in the diagram, see below. The application would keep track of every student's progress of job searching. Through out the sprint I hit into a bunch of roadblocks and with the help of my teammates I was pointed in the right direction.

# Minor tasks
Scaffolded a controller with view for JPStudents
Delete the remove link in the view and deletion function in te controller for the JPChecklist(student should not exist without a checklist)

# Create a viewmnodel for Admin
  - Presents students information base on the viewmodel to the admin
  - Establish a connection to the database from ApplicationUser table to the JPStudent Model then combine those two tables with JPChecklists

# Database Table
![Database Tables](/Images/ViewModelConnectionTable.png)

# Viewmodel
![ViewModel Table](/Images/ViewModelTable.png)

This code snippet is part of what the code is doing. It was hard understanding how to query the database but I learned how to join multiple tables together and present that information to the view. The application count and offercount will be base on the student id that is currently in the query.
##### Note: This code is not finish but it's intent is to query the database and then display the information to the admin

```sh
public class StudentSummaryAdminController : Controller// controller name needs to be the same as the view of StudentSummaryAdmin
    {
        private ApplicationDbContext db = new ApplicationDbContext();

        [HttpGet]
        public ActionResult Index()
        {

            var query = from x in db.JPStudents
                        join y in db.Users //database of ApplicationUsers
                        on x.ApplicationUser.Id equals y.Id //y.applicationuser.id so Id is the primary key of the applicationuser.
                        join z in db.JPChecklists on x.Student_id equals z.Student_id //join JPStudents and JPChecklist
                        select new //get new property values from each student in the database
                        {
                            ApplicationUser_Id = y.UserName,//displays all username //all rows from this table
                            x.Student_id,//get student Id in order to count application count and offer count
                            x.Graduated,
                            x.Hired,
                            z.GitHub, //Properties of JpChecklist
                            z.LinkedIn,
                            z.Portfolio,
                            z.Cover_letter,
                            z.Resume,
                            z.Interview_questions,
                            z.Mock_interview,
                            z.JPCourse_completion
                        };
            var studentVMs = new List<StudentSummaryVM>();//instantiate a new object from the viewmodel - stores in the list

            foreach (var studentSummary in query)//iterates through each objects in the query
            {
                int studentAppCount = db.JPApplications.Where(x => x.Student_id == studentSummary.Student_id).Count();//student Id of JPapplications = student Id then return count -> studentSummary is the object in the query

                var studentVM = new StudentSummaryVM //stores in the list of all the students in the viewmodel


                {
                    StudentName = studentSummary.ApplicationUser_Id,
                    Graduated = studentSummary.Graduated,
                    Hired = studentSummary.Hired,
                    ApplicationCount = studentAppCount,
                    //StudentChecklist = studentSummary
                    //offercount =
                    //graduated =
                };
                studentVMs.Add(studentVM);
            }
            return View(studentVMs);
        }
    }
```
