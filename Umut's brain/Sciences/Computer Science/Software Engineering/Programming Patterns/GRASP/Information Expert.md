# Core principal
*Assign responsibility to the class that has the information needed to fullfil it.*

# The problem it solves
Classes doing workr with data they don't own leads to:
- Anemic domain models (objects are just data containers) 
	- **Anemic** = lacking vitality, weak, bloodless
	- **Objects are just data bags** - no intelligence
- God classes (one class doing everything)
- High coupling (classes knowing too much about each other)
- Scattered logic (business rules everywhere except where they belong)

# The solution
Give each class responsibility for operations involving its own data.

# Key signs that you need it
- Classes asking other classes for data to make decisions
- Lots of getters being called to extract data from processing everywhere
- Service classes doing all the work while domain objects are passive
- Complex methods that manipulate multiple object's data

# Benefits
- Better encapsulation - objects control their own state
- Easier testing - test behaviors independently
- Easier maintenance - changes happen in the right place
- Lower coupling - less dependency on internal details
- More intuitive - responsibilities match real-world expectations

# Quick test
Ask: "*Does this class have the information needed to make this decision?*" If yes, put the responsibility there. If no, find the class that does.

# Examples
> Bad

``` java
class Student {
	private List<Integer> grades;
	public List<Integer> getGrades() { return grades; }
	public void setGrades(List<Integer> grades) { this.grades = grades; }
}

// Logic lives elsewhere
class GradeService {
	public double calculateGrade(Student student) {
		return student.getGrades().stream()
			.mapToInt(Integer::intValue)
			.average()
			.orElse(0.0);
	}
}

```

> Good

``` java
class Student {
	private List<Integer> grades;
	public List<Integer> getGrades() { return grades; }
	public void setGrades(List<Integer> grades) { this.grades = grades; }

	public double calculateGrades() {
		return grades.stream()
			.mapToInt(Integer::intValue)
			.average()
			.orElse(0.0);
	}
}
```