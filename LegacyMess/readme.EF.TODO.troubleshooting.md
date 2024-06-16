If I use Status across multiple tables, I'll get:

Introducing FOREIGN KEY constraint 'FK_Module1.CourseDepartments_Module1.CourseStatus_StatusFK' on table 'CourseDepartments' may cause cycles or multiple cascade paths. Specify ON DELETE NO ACTION or ON UPDATE NO ACTION, or modify other FOREIGN KEY constraints.
Could not create constraint or index. See previous errors.


Unless I put on CascadeDelete(false)...but that's screwy.




