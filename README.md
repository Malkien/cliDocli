# CLI. Docli

## DESCRIPTION
This repository contain a bash script that administrate a workplay of Docker compose. And the diferent utilities of the script are:
    * Create a new empty directory.
    * Run one or all the docker-compose in the workplace.
    * Shut down one or all the docker-compose in the workplace.
    * Display the version.
    * Display the script's information.
Warning: when you run the script remmember that it take as directory you're in, or you can modify it to uso a specify absolute path(but it is you to modify it).

## Table of contents
- [Analysis](#analisys)
- [Desing](#desing)
- [Credits](#credits)

## Analysis
    1. The script have to display a help option that show the information about itslef with the command `--help`.
    2. The script have to have a version of itself and show it woth the command `--version`.
    3. The script have to create a directory only if you pass a context to in.
    4. The script have to boot the docker compose in 1 or all the directories with the correct format, in this case stack_* <-- Numeric.
        * If it's only one pass `--context=name`.
        * If it's all pass `--all`.
    5. The script have to shut down the docker-compose in 1 or all the directories with the same option that the boot have.

## Design
### 1. Create the main of the script.
```shell
{
    case $1 in
        --prueba)
            prueba
            ;;
        --help)
            help
            ;;
        --version)
            version
            ;;
        create)
            create $2
            ;;
        up)
            context_op $1 $2
            ;;
        down) 
            context_op $1 $2
            ;;
        *)
            echo "ERROR TRY WITH: docli [OPTION] ... [DIRECTORY] ..."
            ;;
    esac
} 
```
### 2. Create the help function that display the utilities of the script.
```shell
{
    help(){
        echo "
            NAME
                docli - manage docker-compose stacks centrally
            SYNOPSYS
                docli [OPTION] ... [DIRECTORY] ...
            DESCRIPTION
                Manage multiple docker-compose stacks with a single command.
                --all apply the command to all groups in the inventory
                --context[=CTX] inventory group to apply the command
                --help display this help message and exit
                --version output version information and exit
                The OPTIONS supported include:
                    Create: Creates a new empty directory. Must be followed by
                    the --context flag. The directory name will be the one
                    passed within the context. E.g.:
                    docli create --context=stack_1
                    
                    Up: Passes the \"docker-compose up -d\" command to the
                    specified context. Can be used with --context or --all.
                    docli up --context=stack_1
                    
                    Down: Passes the \"docker-compose down -d\" command to the
                    specified context. Can be used with --context or --all.
                    docli down --all
        "
    }
}
```
### 3. Create the version function that display the version
For this with create a variable in the script that save the version the script is using and is you improve it in the future you only have to change this variable and dont search for where it is used.
```shell
{
    version="1.0.0"
    version(){
	echo "version: $version"
    }
}
```

### 4. Create the create function that create the repository
```shell
{
    #Check if have --context= and if all ok
    #Create a directory
    #@arg = $2
    create(){
        case $1 in 
            --context=*)
                #this take a substring of all after the =
                mkdir "${1##*=}"
                ;;
            *)
                echo "--context=[] expected"
                ;;
        esac
    }
}
```

### 5. Create the funtion context_op
Because the 2 lost funtion operate of the same way better than write 2 time the same thing we create is.

This function use the function in the 6.

```shell
{
    #Check if pass -all or a lone directory and call statement_up_down
    #In -all get all the directories
    #arg=$1
    #arg=$2
    context_op(){
        if [ "$2" == "--all" ];
        then
            #list all the repositories and save only the ones that start for stack_
            list="$(ls -d */ | grep -F stack_)"
            for i in ${list[@]}
            do
                statement_up_down $1 $i
            done	
        else
            i="${2##*=}"
            statement_up_down $1 $i
        fi
    }
}
```

### 6. Create the funtion statement_up_down
This funstion compare if arg 1 is up or down and go inside the directory in arg i execute the up or down and go back to the directory we run the script.
```shell
{
    #Check if is up or down and run the correct command
    #arg=$1 <-- up or down
    #arg=i <-- the directory name
    statement_up_down(){
        if [ "$1" == "up" ]
        then
            cd $i
            docker-compose up
            cd ..
        else
            cd $i
            docker-compose down
            cd ..
        fi
    }
}
```

## Credits
Made by me Malkien ^^