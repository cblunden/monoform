# monoform
Markup language for webforms. Specify complex decision tree webforms in JSON!

## Basic usage
* Forms can have multiple _pages_
* Each page contains _elements_
    * Each element has a type, e.g. _text_ for a text field or _multiselect_, which represents a list of pre-specified
    options (i.e. a dropdown)
    * Elements can have validation. Proceeding to the next page of the form is only possible when the validation has
    been satisfied.


    [
        {
            type: "page",
            name: "applicantDetails",
            label: "Your details",
            elements: [
                {
                    type: "text",
                    name: "firstName",
                    label: "First name",
                    validation: {
                        required: true
                    }
                },
                {
                    type: "text",
                    name: "lastName",
                    label: "Last name",
                    validation: {
                        required: true
                    }
                }
            ]
        },
        {
            type: "page",
            name: "policyDetails",
            label: "Your policy",
            elements: [
                {
                    type: "multiSelect",
                    name: "policyType",
                    label: "Required policy type",
                    options: [
                        {
                            name: "fullCover",
                            label: "Full cover"
                        },
                        {
                            name: "thirdParty",
                            label: "Third party cover"
                        }
                    ]
                }
            ]
        }
    ]

## Conditional display of form elements
### Conditional pages
There are occasions where the user does not need to fill out every page of a form. In this case, it would be useful to
be able to dynamically determine if future pages in the journey are needed and hide them if appropriate. This is
accomplished with the use of the _:visible_ property. When any user input is given into any form field, this property
will be evaluated to determine whether the page it is attached to will be shown or hidden.

In the following example, if the user indicates that they do not want a second applicant on their insurance policy then this
part will be skipped and they will be moved directly onto the "Policy Details" page.

    [
        {
            type: "page",
            name: "applicantDetails",
            label: "Your details",
            elements: [
                {
                    type: "text",
                    name: "firstName",
                    label: "First name",
                    validation: {
                        required: true
                    }
                },
                {
                    type: "text",
                    name: "lastName",
                    label: "Last name",
                    validation: {
                        required: true
                    }
                },
                {
                    type: "multiselect",
                    name: "secondApplicantRequired",
                    label: "Do you wish to include anyone else on your policy?",
                    options: [
                        {
                            value: true
                            label: "Yes - I would like to add another applicant",
                        },
                        {
                            value: false,
                            label: "No - this policy will cover me alone"
                        }
                    ]
                }
            ]
        },
        {
            type: "page",
            name: "secondApplicantDetails",
            label: "Second applicant details",
            ":visible": "applicantDetails.secondApplicantRequired"
            elements: [
                {
                    type: "text",
                    name: "firstName",
                    label: "First name",
                    validation: {
                        required: true
                    }
                },
                {
                    type: "text",
                    name: "lastName",
                    label: "Last name",
                    validation: {
                        required: true
                    }
                }
            ]
        },
        {
            type: "page",
            name: "policyDetails",
            label: "Your policy",
            elements: [
                {
                    type: "multiSelect",
                    name: "policyType",
                    label: "Required policy type",
                    options: [
                        {
                            value: "fullCover",
                            label: "Full cover"
                        },
                        {
                            value: "thirdParty",
                            label: "Third party cover"
                        }
                    ]
                }
            ]
        }
    ]

### Conditional elements

The same _:visible_ property can be applied to individual elements, even if the field upon which the element is
conditional is on the same page.

### Visible vs. disabled

The _:visible_ property will hide pages or hide elements from a page. For instances where this isn't desirable the
_:enabled_ property can be used. If an element is visible but _enabled = false_, the element will be shown but greyed
out.

## Repeating form elements


## Benefits

### Language-agnostic markup means no code repetition
Representing the entirety of a form and its logic in JSON means that a Monoform library could be created for
any language. For example, form JSON could be generated as part of a PHP library, the resulting structure stored in a
NoSQL database and then passed to a React component for rendering. In addition, form validation could be performed
server-side and client-side using the same JSON structure; this eliminates the need to write separate validation logic
for the frontend and backend.

It would also be possible to create a GUI to create forms, which would create and store the form in the Monoform
structure.

It also means the forms could easily be shown in different contexts, for example a web form and a mobile app (provided
that a Monoform rendering library is available).

### Dynamic properties make forms more useful
Putting a semicolon (:) before any property denotes that the property must be evaluated. Evaluation occurs whenever
the user interacts with the form or changes a value. This means that complex trees of conditional pages can be created,
as well as labels and default values which change depending on the form values that have already been completed.
