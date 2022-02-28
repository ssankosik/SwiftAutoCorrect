#! /bin/bash


## Styles
RED='\033[0;31m'
GREEN='\033[0;32m'
# BLUE='\033[0;34m'
NC='\033[0m'
GREEN_BOLD='\033[1;32m'
GREEN_ITALIC='\033[3;32m'


## Consts
save_folder_path="$HOME/Lint"
save_configuration_path="$HOME/Lint/.swiftlint_autocorrect.yml"
save_repository_path="$HOME/Lint/repository.txt"
swift_file_extension="*.swift"
yml_file_extension="*.yml"


## Variables
is_require_repository_path=true
is_require_configuration_path=true
repository_path=""
configuration_path=""
files=()
swift_files=()


## Function - echo
log_fatal_error() {
    echo -e "${RED}(!!) $1${NC}"
    exit 1
}

log_info() {
    echo -e "${GREEN}▶ $1${NC}"
}

detail_log_info() {
    echo -e "${GREEN_ITALIC}$1${NC}"
}

highlight_log_info() {
    echo -e "${GREEN_BOLD}▶ $1${NC}"
}

## Function - echo - extended
log_invalid_input_error() {
    log_fatal_error "Invalid input."
}

log_unknown_error() {
    log_fatal_error "Unknown error."
}

log_complete() {
    log_info "🎉 Done"
    exit 0
}


# Functions
splash() {
echo -e "$GREEN_BOLD"
cat << "EOF"
----------------------------------------
        Swift auto corrector (1.0.0)
----------------------------------------
EOF
echo -e "$NC"
}

has_git_installed() {
    if ! git --version > /dev/null 2>&1; then
        log_fatal_error "Git is not installed."
    fi
    if ! swiftlint version > /dev/null 2>&1; then
        log_fatal_error "SwiftLint is not installed."
    fi
}

setup_repository() {
    # Get exist repository.
    if [ -f "$save_repository_path" ]; then
        repository_path=$(cat "$save_repository_path")
    fi
    if [ ! -d "$repository_path" ]; then
        log_info "Save repository not exist!"
    elif [ ! -d "$repository_path/.git" ]; then
        log_info "Save repository git not exist!"
    fi
    if [ -d "$repository_path/.git" ]; then
        log_info "Found saved repository \"$repository_path\"."
        highlight_log_info "Do you want to continue? (y/n)"
        read -r use_save_repository_path
        case $use_save_repository_path in
            [Yy])
                is_require_repository_path=false
                ;;
            [Nn]) 
                ;;
            * ) 
                log_invalid_input_error
                ;;
        esac
    fi

    # Get new repositoy.
    if [ $is_require_repository_path == true ]; then
        log_info "Input new repository to continue..."
        read -r repository_path
        if [ ! -d "$repository_path" ]; then
            log_fatal_error "Input repository is not valid."
        fi
        if [ ! -d "$repository_path/.git" ]; then
            log_fatal_error "Input repository is not kind of git."
        fi
    fi
    cd "$repository_path" || log_unknown_error
    echo
}

get_modified_list() {
    log_info "Scaning all modified files from repository..."
    modified_file="$(git status -uall --porcelain | sed -ne 's/^ M //p' -ne 's/^A  //p' -ne 's/^AM //p' -ne 's/^?? //p')"
    files=($modified_file)
    if (( ${#files[@]} == 0 )); then
        log_fatal_error "No modified files found from repository."
    fi
    for file in "${files[@]}"; do
        if [[ $file == $swift_file_extension ]]; then
        swift_files+=("$file")
        fi
    done
    if (( ${#swift_files[@]} == 0 )); then
        log_fatal_error "No modified swift files found from repository."
    fi
    for file in "${swift_files[@]}"; do
        detail_log_info "- ${file}"
    done
    log_info "Total: ${#swift_files[@]}"
    echo
}

setup_configuration() {
    # Get exist configuration.
    configuration_path=$save_configuration_path
    if [ ! -f "$configuration_path" ]; then
        log_info "Save configuration not exist, input new configuration to continue..."
    elif [[ $configuration_path != $yml_file_extension ]]; then
        log_info "Save configuration yml not exist, input new configuration to continue..."
    fi
    if [[ $configuration_path == $yml_file_extension ]]; then
        log_info "Found saved configuration ${configuration_path##*/}"
        highlight_log_info "Do you want to continue? (y/n)"
        read -r use_save_configuration_path
        case $use_save_configuration_path in
            [Yy])
                is_require_configuration_path=false
                
                ;;
            [Nn]) 
                ;;
            * ) 
                log_invalid_input_error
                ;;
        esac  
    fi 

    # Get new configuration.
    if [ $is_require_configuration_path == true ]; then
        log_info "Input configuration to continue..."
        read -r configuration_path
        if [ ! -f "$configuration_path" ]; then
            log_fatal_error "Input configuration is not valid."
        elif [[ $configuration_path != $yml_file_extension ]]; then
            log_fatal_error "Input configuration is not kind of yml."
        fi
    fi
    echo
}

start_correction() {
    highlight_log_info "Autocorrect now ready do you want to continue? (y/n)"
    read -r use_autocorrect
    case $use_autocorrect in
        [Yy])
            swiftlint autocorrect --config "$configuration_path" "${swift_files[@]}"
            log_info "Autocorrect complete."
            ;;
        [Nn]) 
            log_complete
            ;;
        * ) 
            log_invalid_input_error
            ;;
    esac
    echo
}

save_configuration() {
    if [ $is_require_repository_path == true ] || [ $is_require_configuration_path == true ]; then
        highlight_log_info "Do you want to save configuration? (y/n)"
        read -r is_save_current_configuration
        case $is_save_current_configuration in
            [Yy])
                rm -rf "$save_folder_path"
                mkdir -p "$save_folder_path"
                cp "$configuration_path" "$save_configuration_path"
                touch "$save_repository_path"
                echo "$repository_path" > "$save_repository_path"
                log_info "Save complete."
                ;;
            [Nn]) 
                ;;
            * ) 
                ;;
        esac
    fi
    log_complete
    echo
}


## Main 
clear
# sleep 1
splash
# Check environment
has_git_installed
# Check repository
setup_repository
# Get files
get_modified_list
# Check configuration
setup_configuration
# Correction
start_correction
# Save configuration
save_configuration


# Asset
# https://github.com/neurobin/shc
# chmod +x autocorrect
# cp autocorrect /usr/local/bin/autocorrect