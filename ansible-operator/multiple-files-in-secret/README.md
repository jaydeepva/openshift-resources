# Introduction
Sometimes instead of using multiple key-value pairs, you may want to store entire file in a secret. If you have multiple files, then you might as well store them in same secret. Mounting the single secret as volume will give your container access to all files stored in the secret.

The ansible task and template demonstrates this. Also included a deployment template that shows how to mount this secret and thus have access to all files within the secret
