CXX = g++-11
CC = gcc-11
CXXFLAGS = -std=c++20
CPPFLAGS = -I/usr/local/include -I/usr/include/eigen3 -I/usr/local/include/osqp -I/home/dcuser/libmpc-0.4.0/include/ -I/home/dcuser/scarab/utils -Wno-deprecated-enum-enum-conversion
CFLAGS = -I /usr/local/include/osqp

# libmpc++ Test
LIBMPC_MAIN = test_libmpc
LIBMPC_SRCS = test_libmpc.cpp
LIBMPC_OBJS = $(LIBMPC_SRCS:.cpp=.o)

# Eigen Test
EIGEN_MAIN = test_eigen
EIGEN_SRCS = test_eigen.cpp
EIGEN_OBJS = $(EIGEN_SRCS:.cpp=.o)

# Linker flags
LDFLAGS = # -L/usr/local/mysql-connector-c++/lib64

# The command "-lm" is from the NLopt documentation.
LDLIBS = -lnlopt -lm -losqp

.PHONY: clean simulate run clean_statistics

run: test_libmpc sim_dir sim_dir/x_c++_to_py sim_dir/u_c++_to_py sim_dir/x_py_to_c++
	./test_libmpc

run_plant: plant_dynamics.py
	export PYTHONPATH="${PYTHONPATH}:.." && ./plant_dynamics.py

# LIBMPC - Run test_libmpc without Scarab. 
$(LIBMPC_MAIN): $(LIBMPC_OBJS)
		$(CXX) -o $(LIBMPC_MAIN) $(LIBMPC_OBJS) $(LDFLAGS) $(LDLIBS)

# Eigen
$(EIGEN_MAIN): $(EIGEN_OBJS)
		$(CXX) -o $(EIGEN_MAIN) $(EIGEN_OBJS) $(LDFLAGS) $(LDLIBS)

# read_input: read_input.cpp ~/scarab/utils/scarab_markers.h 
# 	$(CXX) -o read_input read_input.cpp -O2 -static -I ~/scarab/utils

# Create in/out pipe files for communicating between C++ and Python.
sim_dir/x_c++_to_py: sim_dir
	rm sim_dir/x_c++_to_py
	#  @if ! [ -e "sim_dir/x_c++_to_py" ] ; then\
	mkfifo sim_dir/x_c++_to_py;\
	# fi

# Create in/out pipe files for communicating between C++ and Python.
sim_dir/u_c++_to_py: sim_dir
	rm sim_dir/u_c++_to_py
	#  @if ! [ -e "sim_dir/u_c++_to_py" ] ; then\
	mkfifo sim_dir/u_c++_to_py;\
	# fi

# Create in/out pipe files for communicating between C++ and Python.
sim_dir/x_py_to_c++: sim_dir
	rm sim_dir/x_py_to_c++
	#  @if ! [ -e "sim_dir/x_py_to_c++" ] ; then\
	mkfifo sim_dir/x_py_to_c++;\
	# fi

# Create the simulation directory where the stats from Scarab are generated and the in/out pipe files for communicating between C++ and Python are placed.
sim_dir:
	mkdir sim_dir

# Simulate test_libmpc using Scarab while communicating with the Python ODE solver.
simulate: clean_statistics sim_dir sim_dir/x_c++_to_py sim_dir/u_c++_to_py sim_dir/x_py_to_c++
	./scarab_test_libmpc.py sim_dir

clean_statistics:
	rm -f sim_dir/*.stat.* sim_dir/per_line_* sim_dir/per_branch* sim_dir/uop_queue* ramulator.stat.out

clean:
	rm -f test_libmpc test_libmpc.o sim_dir/* my_pipe
