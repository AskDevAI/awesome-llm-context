 # react-hook-form

 ## useAssistantForm
 
 **Signature**  
 ```ts
 function useAssistantForm<TFieldValues = FieldValues, TContext = any, TTransformedValues = TFieldValues>(
   props?: UseAssistantFormProps<TFieldValues, TContext, TTransformedValues>
 ): UseFormReturn<TFieldValues, TContext, TTransformedValues>;
 ```

 **Description**  
 Custom hook integrating react-hook-form with assistant runtime. It registers form context providers for the assistant to interact with form fields and submission.

 **Import**  
 ```ts
 import { useAssistantForm } from 'assistant-ui/packages/react-hook-form';
 ```

 **Example**  
 ```tsx
 import React from 'react';
 import { useAssistantForm } from 'assistant-ui/packages/react-hook-form';

 type FormData = { firstName: string; lastName: string; };

 const MyForm = () => {
   const { register, handleSubmit } = useAssistantForm<FormData>();
   const onSubmit = data => console.log(data);

   return (
     <form onSubmit={handleSubmit(onSubmit)}>
       <input {...register('firstName')} placeholder="First Name" />
       <input {...register('lastName')} placeholder="Last Name" />
       <button type="submit">Submit</button>
     </form>
   );
 };
 ```

 ## formTools

 **Description**  
 Tools for assistant to interact with form fields and submission.

 **Import**  
 ```ts
 import { formTools } from 'assistant-ui/packages/react-hook-form';
 ```

 ### set_form_field

 **Signature**  
 ```ts
 set_form_field: {
   description: string;
   parameters: { name: string; value: string };
 }
 ```

 **Description**  
 Sets a form field. Call this function as soon as the user provides the data for each field.

 **Example**  
 ```ts
 import { formTools } from 'assistant-ui/packages/react-hook-form';

 // Within assistant tool execution context:
 await formTools.set_form_field.execute({ name: 'email', value: 'user@example.com' });
 ```

 ### submit_form

 **Signature**  
 ```ts
 submit_form: {
   description: string;
   parameters: {};
 }
 ```

 **Description**  
 Submits the form. Confirm with user before submitting.

 **Example**  
 ```ts
 import { formTools } from 'assistant-ui/packages/react-hook-form';

 // Within assistant tool execution context:
 await formTools.submit_form.execute({});
 ```