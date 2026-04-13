#!/usr/bin/env python3
import base64
import argparse
import sys

def convert_to_hashcat(raw_hash):
    try:
        # Sanitize the hash input
        raw_hash = raw_hash.strip()
        
        # Validating if the hash format is correct.
        if not raw_hash.startswith("pbkdf2:sha256:"):
            print(f"[-] Error: The format doesnt looks like a [pbkdf2:sha256] of Werkzeug. Ignored -> {raw_hash[:20]}...")
            return None # Modificado para no romper listas

        # Replacing the $ for : and adding spaces.
        parts = raw_hash.replace('$', ':').split(':')
        
        if len(parts) != 5:
            print(f"[-] Error: Unexpected hash length. Ignored -> {raw_hash[:20]}...")
            return None # Modificado para no romper listas

        _, prefix, iters, salt, hex_hash = parts

        # base64
        b64_salt = base64.b64encode(salt.encode()).decode()
        b64_hash = base64.b64encode(bytes.fromhex(hex_hash)).decode()

        # Assemblying as a 109000 structure
        hashcat_format = f"{prefix}:{iters}:{b64_salt}:{b64_hash}"
        return hashcat_format

    except Exception as e:
        print(f"[-] Unexpected error: {e}")
        return None

def main():
    parser = argparse.ArgumentParser(description="Convert hashes from Werkzeug (Flask) to 10900 format from Hashcat.")
    parser.add_argument("-H", "--hash", help="RAW hash to convert (reference, pbkdf2:sha256:...)")
    parser.add_argument("-f", "--file", help="List of hashes, one per line")
    parser.add_argument("-o", "--output", help="Save the converted hashes to this txt file") # Nuevo feature
    
    args = parser.parse_args()
    converted_hashes = [] # Array to save the results.

    if args.hash:
        res = convert_to_hashcat(args.hash)
        if res:
            print(f"[+] Hashcat (10900 mode):\n{res}\n")
            print(f"Hashcat command ready:")
            print(f"hashcat -m 10900 '{res}' /usr/share/wordlists/rockyou.txt\n")
            
    elif args.file:
        try:
            with open(args.file, 'r') as f:
                for line in f:
                    clean_line = line.strip()
                    if clean_line:
                        res = convert_to_hashcat(clean_line)
                        if res:
                            converted_hashes.append(res)
                            
            print(f"\n[+] Successfully processed {len(converted_hashes)} hashes.")
            
            # If the output file is not defined, print into the console.
            if not args.output:
                for h in converted_hashes:
                    print(h)
                    
        except FileNotFoundError:
            print(f"[-] Error: File Not Found {args.file}")
            sys.exit(1)
    else:
        parser.print_help()
        sys.exit(1)

    # File management for the output
    if args.output and converted_hashes:
        with open(args.output, 'w') as out_f:
            for h in converted_hashes:
                out_f.write(h + '\n')
        print(f"[+] Results saved successfully in: {args.output}\n")
        print(f"Hashcat command ready:")
        print(f"hashcat -m 10900 {args.output} /usr/share/wordlists/rockyou.txt")
        print(f"[🔥] Happy cracking son, kaydycs.")

if __name__ == "__main__":
    main()
