---
title: Parsing Docker Output with jq
summary: The many ways of skinning a cat

excerpt_separator: <!--more-->

tags:
    - powershell
    - json
    - jq
---
There was a question that came up in the [PowerShell discord](https://aka.ms/psdiscord) last week which was an interesting question which ended up with 3 entirely different answers from different people which is interesting to see how different people approach a problem.

### Goal!

```
The goal is to get a list of images from docker images command and delete them with docker rmi
```

<!--more-->

### More than one way to cook an egg
Shane O'Neill ([@Shaneis](https://twitter.com/SOZDBA/)) went for the `-split` operator and some regex to obtain the Name

```powershell
❯❯ $d = docker ps -a
❯❯ $d[1..$d.Count] | ForEach-Object -Process {
>>     [PSCustomObject]@{
>>         Name = ($_ -split '\s{2,}')[-1]
>>     }
>> }

Name
----
ecstatic_chandrasekhar
modest_colden
confident_newton
charming_ganguly
infallible_bassi

```

Jos Koelewijn [(@jawz_84](https://twitter.com/Jawz_84/)) resorted to using `ConvertFrom-StringData` which converts string output into a hashtable so you can access the key/value pairs.

```
❯❯ docker images | Select-Object -Skip 1 | ConvertFrom-StringData -Delimiter ' ' | ForEach-Object Keys
vsc-socialopinions-d9e1feb0b3c073836b87b223deef5934
vsc-azureadfunction-fddb75b17c14d953fe380384c6a1553f
vsc-hacktoberfestscot-e2763e33d037a0d4346af8af7577007a
vsc-stucco-6fc12c4e758f0f1744dacf9375502cd2
vsc-stuccotest-17e1b8eb5a6cf01465f888a61c8384fa
vsc-stuccofix-5998915b92e138a5f4d7cd7c3d3af669
```

### My PowerShell comfort blanket 🛌

PowerShell makes it so easy to perform data manipulation that you very rarely have to use anything else.

I knew that docker had JSON output so I just had the command output the format and use `ConvertFrom-Json`. Using jq and `--slurp` formats individual JSON objects into a large array.
{% raw %}
```bash
docker container ls --all --format '{{json .}}' | jq --slurp | ConvertFrom-Json | Select-Object -Property names
```
{% endraw %}

What about a bit further outside of my comfort zone and not relying on PowerShell? Docker allows you to provide a filter for your format to only return specific properties.
{% raw %}
```bash
docker container ls --all --format '{{.Names}}'
```
{% endraw %}

### And what about using _just_ jq?
You can use the `.[]` object iterator to return only the 
{% raw %}
```bash
docker container ls --all --format '{{json .}}' | jq '.Names'
```
{% endraw %}

Or again using slurp and only selecting the property you require.
{% raw %}
```bash
docker container ls --all --format '{{json .}}' | jq --slurp '.[] | .Names'
```
{% endraw %}

### Conclusion
There are many ways to achieve the same thing and people will generally tackle a problem in the way they're most comfortable with.

I've found over the years that having small amounts of familiarity with different tool sets outside of my normal wheelhouse is beneficial for those moments that you may not have your normal toolset available to you.

Whilst I may have skirted around the goal of removing the containers once you are able to parse the output it can be utilised however you see fit.
