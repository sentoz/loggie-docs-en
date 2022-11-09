# Field Dynamic Variable

In many scenarios, we need to dynamically obtain a field in the event. 
For example:

- The topic in the kafka sink, or the index in the elasticsearch sink, is dynamically generated according to the value of a field in the event. We can use `${a.b}` to get value.
- In transformer interceptor, operate a field: copy(a.b, a.c).

Take the following event is an example:  
```json
{
    "fields": {
        "svc": "test",
    }
}
```

The kafka sink topic is configured as `log-${fields.svc}`, and finally generated as `log-test`.


!!! caution

    You can generally use `.` to denote nested fields. However, if the field itself contains  `.`, it needs to be `[]` enclosed to avoid mistaking it for a nested field. 
    For example:
    ```json
    {
        "fields": {
            "a.b": "demo",
        }
    }
    ```
    Use `${fields.[a.b]}` to represent `a.b`.
