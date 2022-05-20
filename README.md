# Laboratorio_Herramienta_Analisis

USE alfred;

##PARTE 1 
##Como primera parte, se le pide identificar cuál es el alimento que come cada animal según
##su nombre particular, como también, un análisis general que permita visualizar la cantidad de
##animales que hay para cada clase y cuáles de ellas son las que requieren mayor y menor
##inversión en sus alimentos. Luego, determine qué personas son aquellas que alimentan a los
##animales, se necesita saber el nombre de la persona y qué tipo de alimento provee.
##Finalmente, determine cuántos tipos de alimentos se tienen registrados.

SELECT Name
FROM ANIMAL
ORDER BY Name ASC;

##cuál es el alimento que come cada animal segúnsu nombre particular
SELECT Name, FoodType
FROM FEEDING INNER JOIN ANIMAL ON ANIMAL.Species=FEEDING.Species;


##un análisis gral que permita visualizar la cantidad de animales que hay para cada clase
SELECT Class, COUNT(*) AS 'Cantidad de Animales' FROM ANIMAL GROUP BY Class;


##cuáles de ellas son las que requieren mayor y menor inversión en sus alimentos


SELECT Class,AVG(FoodPrice)
FROM ANIMAL INNER JOIN FEEDING ON ANIMAL.Species = FEEDING.Species
GROUP BY CLASS
ORDER BY 2 DESC


##Luego, determine qué personas son aquellas que alimentan a los
##animales, se necesita saber el nombre de la persona y qué tipo de alimento provee.
##Finalmente, determine cuántos tipos de alimentos se tienen registrados.

SELECT ANIMAL.Name, FEEDING.FoodType 
FROM ANIMAL INNER JOIN FEEDING ON ANIMAL.Species = FEEDING.Species
ORDER BY 2 DESC

SELECT FoodType, COUNT(*) AS 'Total tipo alimento'
FROM FEEDING
GROUP BY FoodType
ORDER BY 2 DESC

##r: No existe relación entre persona y tabla de alimento de animales para obtener una respuesta concreta. 

##PARTE 2

##El segundo apartado se relaciona con los trabajadores. Ya han decidido dar bonos a aquellos
##que hayan ingresado antes del año 2000, y otra bonificación extra al trabajador de cuidados
##intensivos. Identifique sus nombres e id.
##Se pide también, una vista general de nombre, salario y actividad específica que desempeñan
##aquellos que la presenten. También, debe determinar qué personas son del staff del
##Zoológico, para ello, se necesita saber el nombre de la persona y a qué departamento
##pertenece. ¿Cuánto dinero se ha pagado por cada tipo de trabajador?


##El segundo apartado se relaciona con los trabajadores. Ya han decidido dar bonos a aquellos
##que hayan ingresado antes del año 2000, y otra bonificación extra al trabajador de cuidados
##intensivos. Identifique sus nombres e id.

SELECT EmployeeFullName, EmployeeID 
FROM EMPLOYEE
WHERE HireDate<'2000-01-01'
ORDER BY HireDate DESC

SELECT EmployeeFullName, EmployeeID, Skill 
FROM EMPLOYEE INNER JOIN ZOOKEEPER ON EMPLOYEE.EmployeeID=ZOOKEEPER.ZEmpID
INNER JOIN ZSKILLS ON ZOOKEEPER.KeeperID=ZSKILLS.KSkillID
WHERE ZSKILLS.Skill = 'Intensive Care'

##Se pide también, una vista general de nombre, salario y actividad específica que desempeñan
##aquellos que la presenten. También, debe determinar qué personas son del staff del
##Zoológico, para ello, se  necesita saber el nombre de la persona y a qué departamento
##pertenece.

SELECT EmployeeFullName, ZOOKEEPER.Salary, ZSKILLS.Skill,EMPLOYEE.EmployeeID , STAFF.Department  
FROM EMPLOYEE
LEFT OUTER JOIN ZOOKEEPER ON EMPLOYEE.EmployeeID=ZOOKEEPER.ZEmpID
LEFT OUTER JOIN ZSKILLS ON ZOOKEEPER.KeeperID=ZSKILLS.KSkillID
LEFT OUTER JOIN	(
 	SELECT OEmpID , Department 
 	FROM OSTAFF 
 	)AS STAFF ON EMPLOYEE.EmployeeID = STAFF.OEmpID 


##¿Cuánto dinero se ha pagado por cada tipo de trabajador?
 	
SELECT ZOOKEEPER.Salary, ZSKILLS.Skill 
FROM ZOOKEEPER INNER JOIN ZSKILLS ON ZSKILLS.KSkillID=ZOOKEEPER.KeeperID 
ORDER BY Salary  DESC 

##PARTE III
El tercer ítem se vincula con el tipo de persona que visita el Zoológico. A diferencia de los
trabajadores, en este apartado aún no han podido tomar grandes decisiones, por ello, se le
pide vislumbrar con una estadística básica, ¿qué segmento de personas habría que atraer
respecto a sus características demográficas y valor recaudado?¿respecto a cuáles habría
que desistir? Sea detallista, muestre la información a nivel general y específica.

SELECT VisitorAge as 'EDAD',
City as 'CIUDAD', 
TicketPrice AS 'PRECIO TICKET', 
TicketType AS 'TIPO TICKET',
PERSON.US_State AS 'ESTADO'
FROM VISITOR INNER JOIN PERSON ON VISITOR.VisitorFullName =PERSON.FullName
ORDER BY TicketPrice DESC 

##Segmento de personas de acuerdo a valor recaudado 
SELECT VisitorCategory,PERSON.US_State, SUM(TicketPrice),COUNT(VisitorCategory)
FROM VISITOR INNER JOIN PERSON ON VISITOR.VisitorFullName = PERSON.FullName
GROUP BY VisitorCategory, PERSON.US_State 
ORDER BY 3 DESC

##¿Cuál es el ticket promedio de los visitantes de aquel Estado donde más visitan el Zoológico?

SELECT PERSON.US_State, COUNT(*)
FROM VISITOR INNER JOIN PERSON ON VISITOR.VisitorFullName = PERSON.FullName
GROUP BY PERSON.US_State
ORDER BY 2 DESC


SELECT US_State AS ESTADO, AVG(VISITOR.TicketPrice) AS TICKET_PROMEDIO
FROM VISITOR INNER JOIN PERSON ON VISITOR.VisitorFullName = PERSON.FullName
WHERE US_State='Massachusetts'

##¿En qué Estado se encuentran los adultos que más van al Zoológico?
SELECT PERSON.US_State, VisitorCategory, COUNT(*)  
FROM VISITOR INNER JOIN PERSON ON VISITOR.VisitorFullName = PERSON.FullName
WHERE VisitorCategory ='ADULTO/A'
GROUP BY PERSON.US_State
ORDER BY 3 DESC

##Además, se le comenta que todos los años se hace una invitación especial a aquellos
##visitantes con tipo de ticket diario, para persuadir su suscripción anual. ¿En qué ciudad se
##encuentra la mayoría de los invitados?

SELECT TicketType AS TIPO_DE_TICKET, PERSON.City AS CIUDAD, COUNT(*)
FROM VISITOR INNER JOIN PERSON ON VISITOR.VisitorFullName = PERSON.FullName
WHERE TicketType  ='Day'
GROUP BY PERSON.City  
ORDER BY 3 DESC


