# Using Secrets Manager with Cloud Build (For App Engine apps)

This set-up guide is useful for projects that are deployed to App Engine, since App Engine does not have a direct implementation with Secrets Manager.


> 💡 Original Recording by Sean:
https://us06web.zoom.us/rec/share/Rta443VmLoqMuGKBAug6GSWqnNXSeEhKsnfHktqNlhV5bqcYBwIKEIF9AW-ZvoLK.Zew9nbwaxlYB0X9r
> 
> Passcode:
> 
> `f$AQB8#Y`



### This doc assumes you already have the following:

- An existing ***GCP Project*** that already has ***Cloud Build*** setup
    - Including a ***Cloud Build Trigger*** created and linked to a YAML cloudbuild file in your project repository
        - You will need to take note of the **Service Account Email** of the Trigger
- You have **Owner** or **Editor** Access to the project

## Step by step guide

1. Go to ***Google Cloud Console*** and Select the Project you need to setup. And enable ***Secrets Manager API***
2. Add your secrets on the ***Secrets Manager***
    1. Secret Names should be what your application would be calling `process.env.SECRET_NAME`
3. Go to ***IAM*** and look for the  **Service Account Email** used in the ****Cloud Build Trigger that will need your Secret values. And add the **Secret Manager Secret Accessor** role to them**.**
4. Go to the project code, and look for the `cloudbuild YAML file` linked to the Cloud Build Trigger that will need your Secret values
5. Add this to the end of the YAML file.

    ```yaml
    # replace the values and add or remove more secrets based on what you need
    # You don't have to update the $PROJECT_ID field, it's value will come from 
    # cloud build using substitutions.*
    availableSecrets:
      secretManager:
        - versionName: projects/${PROJECT_ID}/secrets/SECRET_NAME_1/versions/latest
          env: 'SECRET_NAME_1'
        - versionName: projects/${PROJECT_ID}/secrets/SECRET_NAME_2/versions/latest
          env: 'SECRET_NAME_1'
    ```

   > *If you are wondering what `latest` is, in secrets manager you can have more than 1 version of a secret, in this case we are specifying that we use the latest value of the secret. You can read more about it in* [Add a secret version](https://cloud.google.com/secret-manager/docs/add-secret-version)

6. In your `cloudbuild.yaml` file, find the build step that needs the secret values add this step option.

   > Commonly in our projects this is the build step that uses the `create-env` script, but this can be applied to every build step that may need the secrets.

    ```js
    name: 'gcr.io/cloud-builders/npm'
      args: ['run', 'create-env', 'dist/apps/backend/.env']
      # This will load the secrets from secrets manager into the runtime environment
      # And as a result, it will be included when the create-env script is ran
      secretEnv: 
        ['SECRET_NAME_1', 'SECRET_NAME_2']
    ```


Now you no longer need to add these values to the Cloud Build Trigger. And these Secrets can be reused on other Cloud Build Triggers in the same project!