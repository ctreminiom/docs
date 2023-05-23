# PII Data Check

To check if a string contains Personally Identifiable Information (PII) information across all categories using Go, you can leverage regular expressions and pattern matching. Keep in mind that PII detection can be a complex task, and it is highly recommended to consult legal and privacy experts for your specific use case.

Here's an example implementation that demonstrates how you can detect PII information using regular expressions in Go:

```go
package main

import (
	"fmt"
	"regexp"
)

func containsPII(text string) bool {
	piiPatterns := map[string]string{
		"Email":    `\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b`,
		"Phone":    `\b(?:\+?\d{1,3}[.-]?)?\(?\d{3}\)?[.-]?\d{3}[.-]?\d{4}\b`,
		"SSN":      `\b\d{3}-\d{2}-\d{4}\b`,
		"Address":  `\b\d+\s+([A-Za-z]+|[A-Za-z]+\s[A-Za-z]+)\b`,
		"CreditCard": `(?:(?:4\d{3}|5[1-5]\d{2}|6011|3[47]\d{2})[- ]?)?(?:\d{4}[- ]?){3}\d{4}\b`,
	}

	for category, pattern := range piiPatterns {
		re := regexp.MustCompile(pattern)
		if re.MatchString(text) {
			fmt.Printf("Found PII in category %s\n", category)
			return true
		}
	}

	return false
}

func main() {
	text := "John Doe, 123 Main St, New York, NY 10001, johndoe@example.com, (555) 123-4567, 123-45-6789, 4111 1111 1111 1111"
	if containsPII(text) {
		fmt.Println("PII information detected.")
	} else {
		fmt.Println("No PII information found.")
	}
}
```

In this example, we define a `containsPII` function that checks if a given `text` string contains PII information. The function uses a map of PII patterns, where each key represents a PII category, and the value is the regular expression pattern to match against the text.

The example includes some common PII categories such as Email, Phone, Social Security Number (SSN), Address, and Credit Card. You can customize these patterns according to your requirements or add additional patterns for other PII categories.

The `containsPII` function iterates over the patterns, uses the `regexp` package to compile each pattern into a regular expression, and checks if the pattern matches the input text. If a match is found, it prints the corresponding category and returns `true`. Otherwise, it returns `false`.

In the `main` function, we demonstrate the usage of the `containsPII` function by passing a sample `text` string. Based on the result, it prints whether PII information was detected or not.

Remember to review and adjust the regular expressions and patterns based on your specific needs, as this example covers only a few basic PII categories.

## Regular Expressions

Extracting sensitive information (Personally Identifiable Information or PII) using regular expressions can be a complex task since PII can vary in format and structure depending on the specific type of information. However, here is a list of regular expressions commonly used to extract different types of sensitive information:

### Email Address:

```
\b[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}\b
```

### Phone Number (US Format):

```
\b(?:\+?1[-.])?(?:\([2-9]\d{2}\)|[2-9]\d{2})[-.]\d{3}[-.]\d{4}\b
```

### Credit Card Number (Generic):

```
\b(?:\d[ -]*?){13,16}\b
```

### Social Security Number (US Format):

```
\b\d{3}[-]?\d{2}[-]?\d{4}\b
```

### Driver's License Number (US Format):

```
\b[A-Z]{1}\d{6}\b
```

### Passport Number

```
\b[A-Z]{1}[0-9]{7}\b

```
