---
title: 
date: "2021-03-25T19:49:17+0800"
draft: false
categories: ["cd/cd"]
---


[Pipeline Syntax](https://www.jenkins.io/doc/book/pipeline/syntax)

## ci/cd

continuous  integration and  continuous delivery

1. continuous  integration
    1. test
    2. build

2. continuous delivery
   1. deploy

## jenkins

## build way

1. freestyle

   1. scm 2. build trigger

2. pipeline
    a set of sh steps

### pipeline  declarative

```jenkinsfile

agent any

environment  {
    Hello =
}

stage1 {

    steps (
        sh "cd ~/"
        sh '''
        mkdir  abc
        mv abc cde
        '''
    )
}


stage2{

}


#finish hook
post {
    always {
    
    }
    success {

    }
    failure {
        
    }

}

```

### pipeline  script

provide more flexible and expressive control over the pipeline

```
node {
    stage('Exam`ple') {
        if (env.BRANCH_NAME == 'master') {
            echo 'I only execute on the master branch'
        } else {
            echo 'I execute elsewhere'
        }
    }
}
```

### blue ocean

develop the user experience for pipeline, but still compatiable with free style jobs
