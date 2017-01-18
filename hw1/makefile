CC=gcc
CFLAGS=-Wall -g

sh: sh.c
	$(CC) $(CFLAGS) $^ -o $@

test: sh
	@echo "-- test 0: --"
	cat t.sh  | ./sh
	@echo "-- test 1: --"
	cat t1.sh | ./sh
	@echo "-- test 2: --"
	cat t2.sh | ./sh

clean:
	$(RM) sh
