## Creating Data transfer object (DTO)

Use them where data coming from the world outside. Convert that data into a structure (DTO)
that the application can work with.
Data transfer object (DTO):
- They don't need constructor or may use simple regular constructor
- Its properties can be set one by one.
- All of its properties are exposed (public).
- No need for getters and setters
- Use property fillers when needed
- Its properties contain only primitive-type values.
- Properties can optionally contain other DTOs, or simple arrays of DTOs.

Exp.
```php
Request request
formData = /* ... */;
scheduleMeetup = new ScheduleMeetup();
scheduleMeetup.title = formData['title'];
scheduleMeetup.date = formData['date'];

this.scheduleMeetupService.execute(scheduleMeetup);
```

Don’t throw exceptions, collect validation errors
```php
final class ScheduleMeetup
{
    public string title;
    public string date;
    public function validate(): array
    {
        errors = [];
        if (this.title == '') {
            errors['title'][] = 'validation.empty_title';
        }
        if (this.date == '') {
            errors['date'][] = 'validation.empty_date';
        }
        DateTime.createFromFormat('d/m/Y', this.date);
        errors = DateTime.getLastErrors();
        if (errors['error_count'] > 0) {
            errors['date'][] = 'validation.invalid_date_format';
        }
        return errors;
    }
}
```