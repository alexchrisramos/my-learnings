Chart - includes all the necessary objects to deploy an application in kubernetes - deployment, svc, secret, cm's

repo's can be found in hubs
artifact hub, kubeapps hub

helm repo add https://charts.bitnami.com/bitnami

searching
helm search hub
helm search repo drupal
helm search repo drupal -l #versions (chart version and application version)

installing
helm install my-release bitnami/drupal

installing with parameters found in documentation
helm install my-release2 --set drupalUsername=myusername --set drupalPassword=password bitnami/drupal

install specific chart versions
helm install myrabbitmq bitnami/rabbitmq --version 6.18.3

get information from specific release
helm status releasename

upgrade helm releases - even if not upgrading the chart version but only adding/modifying a parameter
helm upgrade myrabbitmq bitnami/rabbitmq --version 6.18.3 --set replicas=3 (and other parameters required stated in the chart docs)
helm --kubeconfig ${CLUSTER_NAME}.conf upgrade rabbitmq bitnami/rabbitmq --set replicas=5 --set auth.password=JrBR6g3zg3 --set auth.erlangCookie=dT7BUxBlwd4Nh5dAXB5zboauIXGT9Ul6



creating charts
helm create <chartname>
$ tree samplechart/
samplechart/
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── deployment.yaml
│   ├── hpa.yaml
│   ├── ingress.yaml
│   ├── service.yaml
│   ├── serviceaccount.yaml
│   └── tests
│       └── test-connection.yaml
└── values.yaml

3 directories, 10 files

chart.yaml contains the metadata of the chart
    appVersion - the version of the application
values.yaml
    value of parameters
charts directory
    contains charts that this charts depends on
templates directory
    contains the definitions objects
notes file
    the text message after installation of the release


lets remove the default *.yaml and dir tests inside the template
and make some jenkins deployment files

$ tree samplechart/
samplechart/
├── Chart.yaml
├── charts
├── templates
│   ├── NOTES.txt
│   ├── _helpers.tpl
│   ├── jenkins-deployment.yaml
│   └── jenkins-service.yaml
└── values.yaml

2 directories, 6 files


GO templating
values of specific objects in the template yaml files can be converted so that it can retrieve that from the values.yaml

ex. spec.containers.image

    spec:
      containers:
      - name: jenkins
        image: jenkins/jenkins:latest

will become

    spec:
      containers:
      - name: jenkins
        image: {{ .Values.image.repository }}:{{ .Values.image.tag }}

note: --set flag in the helm cli will supersede any values set

also, the name needs to be unique
the release name can be accessed using .Release (must be unique)

{{ .Release.Name }}

it also a common to practice to append the name of the chart
{{ .Release.Name }}--{{ .Chart.Name }}


then update the values.yaml file

image:
  repository: bitnami/jenkins
  pullPolicy: IfNotPresent
  # Overrides the image tag whose default is the chart appVersion.
  tag: latest


helm template RELEASENAME <dir of chart>
#if you cd on the chart dir, then 
helm template RELEASENAME .

this will show you the template outcome when rendered

limit output to certain template
helm template MYRELEASE . -s /template/<name of template>.yaml

also inside the template directive - double curly braces
you can call a GO function

{{ printf "%s-%s" .Release.Name .Chart.Name }}
similarly
{{ printf "%s:%s" .Values.image.repository .Values.image.tag }}


other GO function that can be used is - default
Example:

    spec:
      containers:
      - name: jenkins
        image: {{ printf "%s:%s" .Values.image.repository .Values.image.tag }}
        env: 
          - name: JENKINS_USERNAME
            value: {{ default "test" .Values.jenkinsUsername.value | quote}}

this should default to "test" if no value is provided for jenkinsUsername.value and will quote it to prevent whitespaces
to give a value, use the --set flag
helm template MYRELEASE . --set jenkinsUsername.value="alex" -s templates/jenkins-deployment.yaml

Other functions can also be used, like if else
Example:
       env: 
          - name: JENKINS_USERNAME
            value: {{ default "test" .Values.jenkinsUsername.value | quote}}
          - name: JENKINS_PASSWORD
            {{- if .Values.jenkinsPassword.value -}}
            value: {{ .Values.jenkinsPassword.value | quote}}
            {{- else }}
            value: testPassword
            {{- end }}


note: the minus sign before (even after) the function instructs helm to remove any character before and after (left side or right side)

Another function that is usable is Range function
Example:
        ports:
        {{- range .Values.containerPorts -}}
        - name: {{ .name }}
          containerPort: {{ .port }}
        {{- end -}}


Template macros are stored in _helpers.tpl
these are pieces of code that can be reused thoughout the template
Example:
name: {{- template "samplechart.fullname" . -}}
which is defined in the _helpers.tpl


for creating secret, it has to be base64 encoded
Example:
data:
  {{- if empty .Values.accessKey }}
  access-key: {{ randAlphaNum 10 | b64enc | quote }}
  {{- else }}
  access-key: {{ .Values.accessKey | b64enc | quote }}
  {{- end }}

  {{- if empty .Values.secretKey }}
  secret-key: {{ randAlphaNum 10 | b64enc | quote }}
  {{- else }}
  secret-key: {{ .Values.secretKey | b64enc | quote }}
  {{- end }}
kind: Secret
metadata:
  name: {{ include ""minio.fullname" . }}
  labels:
    {{- include "minio.labels" . | nindent 4 }}


in the notes, the message is printed, where usually the URL and access key's are provided
to generate this




