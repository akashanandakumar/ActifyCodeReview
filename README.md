# Code Review comments

## Bugs

### 1. Form submit (HTTP POST request) success execution path is continued to error

Shall we differentiate the execution paths for success and error? The current code call both success and error when in success.

```javascript
	async handleButtonClick() {
		if (this.validateForm()) {
			try {
				const res = ...;
				if (res.status === 200) {
						...
				} 
				else {
					const feedback = 'Email Not Sent';
					const success = false;
					this.setState({feedback, success });
				}
			} catch (err) {
				console.log(err);
			}
	}
}
```

### 2. Using 'feedback' variable to check if form submission success lead to unusual behaviour

When rendering the component, using `feedback:string` which compares with boolean value leads to undefined behaviour. It ideal to do boolean operations against booleans.
Shall we use `success` variable instead?

(Because of this undefined behaviour, initially we will not see the form.)

```javascript
	
	render() { ...

            // return ( ...
            //     {!feedback ?
            //         ...
			// 		:
			// 			{success &&
			// 				...
			// 			}
			// 	}
			// );
			return ( ...
                {!success ?
                    ...
					// render form
					:
					...	
					// render success message
				}
			);
	}
```

## Improvements

### 1. Default field values

Instead of using a space for default value, shall we use a empty string?
Having an empty value cause the length validation to pass by form email and subject variables.

```javascript
	constructor (props) {
			super(props);
			this.state= {
					//email: ' ',
					email: '',
					validEmail: true,
					...
			};
	}
```


### 2. Handle fetch exceptions with proper user feedback 

In case of network errors, the exception thrown from fetch method is only logged. We could provide a feedback to user.

```javascript
async handleButtonClick() {
	if (this.validateForm()) {
		try {
			//
		} catch (err) {
			this.setState({ feedback: "Error submitting form. Try again."})
			console.log(err);
		}
	}
}

renderForm() {
	return (
		...

		<div>{feedback}</div>

		...
	)
}
```

### 3. Using standard name conventions 

Shall we use standard name conventions for variables?
Using snake_case for `validate_message` goes against the convention being followed in the script.

```javascript
	constructor (props) {
		super(props);
		this.state= {
			...
			validEmail: true,
			validSubject: true,
			// valid_message: true,
			validMessage: true,

		};
	}
```

## Readability 


### 1. Using extract render() for validation

shall we extraxt the form and success message section into separate render() function. That way, we can improve readability of the excution pathways of the render() function.


```javascript
	render() {
		...
		return (
			{success ?
				renderSuccess()
				:
				renderForm()
			}
		)
	}

	renderForm() {
		...
		return (
			...
			FormGroup
			...
		)
	}
	renderSuccess() {
		...
		return (
			...
			Success
			...
		)
	}


```

### 2. Use gard clauses

Shall we avoid deep nesting by terminating early if validation fails?

```javascript
async handleButtonClick() {
	if (!this.validateForm()) {
		return;
	}

	// submit form code 
}
```
