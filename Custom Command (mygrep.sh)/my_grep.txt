
# Function to show usage
usage() {
  echo "Usage: $0 [-n] [-v] search_string filename"
  echo "Options:"
  echo "  -n    Show line numbers"
  echo "  -v    Invert match (show lines that do not match)"
  echo "  --help Show this help message"
  exit 1
}

# Check for --help
if [[ "$1" == "--help" ]]; then
  usage
fi

# Initialize variables
show_line_numbers=false
invert_match=false

# Parse options
while getopts ":nv" opt; do
  case $opt in
    n) show_line_numbers=true ;;
    v) invert_match=true ;;
    \?) echo "Invalid option: -$OPTARG" >&2; usage ;;
  esac
done

shift $((OPTIND -1)) # Remove parsed options

# Check if enough arguments are left
if [ $# -lt 2 ]; then
  echo "Error: Missing search string or filename."
  usage
fi

search_string="$1"
file="$2"

# Check if file exists
if [ ! -f "$file" ]; then
  echo "Error: File '$file' not found."
  exit 1
fi

# Set grep options
grep_options="-i"
if $invert_match; then
  grep_options="$grep_options -v"
fi
if $show_line_numbers; then
  grep_options="$grep_options -n"
fi

# Run grep
grep $grep_options -- "$search_string" "$file"
